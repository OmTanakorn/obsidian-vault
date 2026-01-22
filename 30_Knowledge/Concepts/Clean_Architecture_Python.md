---
type: knowledge
topic: Backend Architecture
tags: [backend, python, fastapi, clean-architecture, design-patterns]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# Clean Architecture with Python (FastAPI)

## 📌 Concept
การออกแบบ Software แบบ **Clean Architecture** (หรือ Onion Architecture) เน้นการแยก Business Logic ออกจาก Framework และ Database โดยใช้กฎของ **Dependency Inversion** เพื่อให้ระบบมีความ Flexible, Testable และ Scalable

### 4 Layers of Isolation
1. **Domain (Inner)**: Business Logic & Entities (Pure Python)
2. **Use Cases (Application)**: กฎการทำงานของระบบ (Orchestration)
3. **Adapters (Interface)**: ตัวเชื่อมโลกภายนอก (Repositories, Controllers)
4. **Infrastructure (Outer)**: Framework (FastAPI), DB (SQLAlchemy), External APIs

## 💻 How it works / Code

### 📂 Folder Structure
```text
src/
├── domain/             # Logic เพียวๆ (ห้ามนำเข้า Library ภายนอก)
├── use_cases/          # กฎของระบบ และ Interfaces (Ports)
├── adapters/           # Repository Implementation & Controllers
└── main.py             # Dependency Injection & Framework Setup
```

### 🛠 ตัวอย่าง Code (Abstraction & Use Case)

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass

# 1. Domain Entity
@dataclass
class User:
    username: str
    email: str

# 2. Port (Interface)
class UserRepository(ABC):
    @abstractmethod
    def save(self, user: User) -> User: pass

# 3. Use Case (Dependency Injection)
class RegisterUserUseCase:
    def __init__(self, repo: UserRepository):
        self.repo = repo

    def execute(self, username: str, email: str) -> User:
        # Business logic goes here
        user = User(username=username, email=email)
        return self.repo.save(user)
```

## 🔗 References
- [Clean Architecture by Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [FastAPI Dependency Injection](https://fastapi.tiangolo.com/tutorial/dependencies/)
