---
type: knowledge
topic: Django REST Framework Architecture
tags: [django, drf, backend, architecture, service-layer]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# Django REST Framework: The Service Layer Pattern

## 📌 Concept
Django มีความเป็น "Opinionated Framework" สูง การใช้ Clean Architecture แบบเต็มสูบ (แยก Domain ขาดจาก ORM) มักจะทำให้เขียนยากและเสีย Performance
แนวทางที่เหมาะสมที่สุดสำหรับ Django ระดับ Enterprise คือ **Service Layer Pattern** (หรือ Styleguide Pattern) ที่เน้นแยก **Write Logic** และ **Read Logic** ออกจาก Views และ Models

## 📂 Project Structure
แบ่งตาม **Django Apps** (1 App = 1 Domain) และแตกไฟล์ภายในเพื่อแยกหน้าที่

```bash
backend_django/
├── 📂 apps/
│   ├── 📂 users/          # Domain Module
│   │   ├── 📂 api/        # Presentation (Views & Serializers)
│   │   │   ├── views.py
│   │   │   └── serializers.py
│   │   ├── models.py      # Database Schema (Fat Models for data, not logic)
│   │   ├── services.py    # 🔴 WRITE Logic (Create, Update, Actions)
│   │   ├── selectors.py   # 🔵 READ Logic (Complex Queries)
│   │   └── urls.py
```

## 💻 Key Components

### 1. Services (`services.py`)
ใช้สำหรับ Business Logic ที่มีการเปลี่ยนแปลงข้อมูล (Mutation)
*กฎ: Function หนึ่งควรทำงานหนึ่งอย่างให้จบ (Atomic)*

```python
def register_user(*, email: str, password: str) -> User:
    with transaction.atomic():
        user = User.objects.create_user(email=email, password=password)
        # Call other services
        email_service.send_welcome(user)
    return user
```

### 2. Selectors (`selectors.py`)
ใช้สำหรับดึงข้อมูล (Query) ที่ซับซ้อน เพื่อให้ Reuse ได้และ Views ไม่รก
*กฎ: Return เป็น QuerySet หรือ List of Objects*

```python
def get_premium_users() -> QuerySet[User]:
    return User.objects.filter(is_active=True, plan='premium')
```

### 3. API Views (`api/views.py`)
ทำหน้าที่เป็น Interface เท่านั้น (รับ Input -> เรียก Service -> ส่ง Output)

```python
class RegisterApi(APIView):
    def post(self, request):
        serializer = InputSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        
        # Call Service
        register_user(**serializer.validated_data)
        
        return Response(status=201)
```

## 🆚 Comparison with Clean Arch (FastAPI)
| Feature | Clean Arch (FastAPI) | Service Layer (Django) |
| :--- | :--- | :--- |
| **Data Models** | Pure Entities (No SQL) | Django ORM Models |
| **Logic Location** | Use Case Classes | Service Functions |
| **DB Dependency** | Inverted (Interface) | Direct (ORM Usage) |
| **Pros** | Framework Independent | Faster Dev, Idiomatic Django |

## 🔗 References
- [Django Styleguide by HackSoftware](https://github.com/HackSoftware/Django-Styleguide)
