---
type: knowledge
topic: FastAPI Architecture
tags: [fastapi, python, clean-architecture, project-structure, backend]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# Production-Grade FastAPI Clean Architecture

## 📌 Concept
โครงสร้างโปรเจกต์ FastAPI แบบ **Full Clean Architecture** ที่เน้นความชัดเจนในการแยกหน้าที่ (Separation of Concerns) เหมาะสำหรับระบบขนาดกลางถึงใหญ่ (Enterprise) โดยแยก Layer อย่างเคร่งครัด: **Domain, Use Case, Infrastructure, Presentation**

## 📂 Project Structure

```bash
backend_project/
├── 📂 src/
│   ├── 📂 api/                 # 🟢 Presentation Layer (FastAPI Routers)
│   │   ├── 📂 v1/
│   │   │   ├── 📂 endpoints/   # URL Handlers
│   │   │   └── api.py          # Route Aggregator
│   │   └── deps.py             # 🔌 Dependency Injection (Wiring Root)
│   │
│   ├── 📂 core/                # Global Config
│   │   ├── config.py           # Env Vars (Pydantic Settings)
│   │   ├── security.py         # JWT, Hashing
│   │   └── exceptions.py       # Custom Error Handlers
│   │
│   ├── 📂 domain/              # 🟡 Layer 1: Enterprise Rules (Pure Python)
│   │   ├── 📂 user/
│   │   │   ├── entity.py       # Dataclass (No SQL/Pydantic)
│   │   │   └── repository.py   # Interface (Abstract Base Class)
│   │
│   ├── 📂 use_cases/           # 🔴 Layer 2: Business Rules
│   │   ├── 📂 user/
│   │   │   ├── register.py     # Service Logic (Interactor)
│   │   │   └── dtos.py         # Pydantic Schemas (Input/Output)
│   │
│   ├── 📂 infrastructure/      # 🔵 Layer 3: Frameworks & Drivers
│   │   ├── 📂 db/
│   │   │   ├── session.py      # DB Connection
│   │   │   └── 📂 models/      # SQLAlchemy ORM Models (Table Defs)
│   │   └── 📂 repositories/
│   │       └── user_repo.py    # Implementation (SQLAlchemy)
│   │
│   └── main.py                 # App Entry Point
│
├── 📂 tests/
├── .env
├── alembic.ini                 # DB Migrations
└── pyproject.toml              # Dependencies
```

## 💻 Key Implementation Details

### 1. Domain (Purest)
*ห้ามมี Library ภายนอก ห้ามมี SQL ห้ามมี HTTP*
```python
# src/domain/user/entity.py
@dataclass
class User:
    id: UUID
    email: str
    is_active: bool = True
```

### 2. Use Cases (Logic Orchestrator)
*ใช้ DTO รับส่งข้อมูล และเรียก Interface ของ Repository*
```python
# src/use_cases/user/register.py
class RegisterUserUseCase:
    def __init__(self, repo: UserRepository):
        self.repo = repo

    def execute(self, data: UserRegisterDTO) -> UserResponseDTO:
        if self.repo.get_by_email(data.email):
            raise EmailAlreadyExistsError()
        # ... logic ...
```

### 3. Infrastructure (Real World)
*แปลงจาก ORM Model -> Domain Entity ที่นี่*
```python
# src/infrastructure/repositories/user_repo.py
class PostgresUserRepository(UserRepository):
    def save(self, user: User) -> User:
        orm_model = UserModel(id=user.id, email=user.email)
        self.db.add(orm_model)
        self.db.commit()
        return orm_model.to_entity()
```

### 4. Dependency Injection (`deps.py`)
*จุดเชื่อมต่อ (Wiring) ที่ทำให้สลับ Infrastructure ได้ง่าย*
```python
# src/api/deps.py
def get_register_use_case(db: Session = Depends(get_db)):
    repo = PostgresUserRepository(db)
    return RegisterUserUseCase(repo)
```

## 💡 Why this structure?
1.  **DTO vs ORM vs Entity**: แยกกันชัดเจน ไม่ปนกันมั่ว
2.  **Testability**: Test Logic ง่ายมากแค่ส่ง FakeRepo เข้าไปใน Use Case
3.  **Scalability**: เพิ่ม Feature ใหม่ก็แค่เพิ่ม Use Case ไม่กระทบส่วนอื่น
