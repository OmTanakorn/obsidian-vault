# PayStation Backend Codebase Summary

**Generated:** 2026-04-15
**Location:** `/home/tanakorn/Documents/workspace/paystation-backend/`

---

## Overview

PayStation Backend v2 is a **Django 5.2 + DRF** backend service for managing payment claims, projects, RVO (Request Verification Order) workflows, and company management. The system integrates with **SAP ERP** via OData APIs, uses **PostgreSQL** as the primary database, **Redis** for caching/sessions, and **Celery** for async tasks.

### Tech Stack

| Component | Technology |
|-----------|-----------|
| Framework | Django 5.2, Django REST Framework 3.16 |
| Database | PostgreSQL 15 |
| Cache/Session | Redis 7 |
| Task Queue | Celery 5.5 + Beat |
| WebSockets | Django Channels 4.3 |
| API Docs | drf-spectacular (OpenAPI 3.0) |
| Auth | Azure Entra SSO (OAuth2/OIDC), django-oauth-toolkit |
| Storage | AWS S3 (django-storages) |
| Notifications | Firebase FCM, Email (async via Celery) |
| Monitoring | OpenTelemetry, Django Silk (dev) |
| Package Manager | uv (Python 3.13) |

---

## Project Structure

```
paystation-backend/
├── manage.py                    # Django entry point
├── pyproject.toml               # Dependencies (uv)
├── requirements.txt             # Pip requirements (CI/CD)
├── schema.yml                   # OpenAPI spec
├── docker-compose.yml           # Local dev (PostgreSQL + Redis)
├── .claude/                     # Agent workspace (plans, docs, memory)
├── paystation_backend/          # Main Django app
│   ├── settings.py              # Main settings (imports modular settings)
│   ├── urls.py                  # Root URL routing
│   ├── celery.py                # Celery app config
│   ├── asgi.py                  # ASGI for Channels
│   └── other_settings/          # Modular settings
│       ├── core.py              # Core Django configs
│       ├── redis.py             # Cache/session
│       ├── sap.py               # SAP integration
│       ├── sso.py               # Azure SSO
│       ├── storage.py           # S3 storage
│       ├── email.py             # Email backend
│       ├── firebase.py          # FCM push notifications
│       ├── logging.py           # Logging config
│       ├── opentelemetry.py     # Distributed tracing
│       ├── swagger.py           # API documentation
│       ├── channel.py           # Django Channels
│       ├── session.py           # Session management
│       └── pdf_template.py      # PDF generation
└── apis/                        # Business logic apps
    ├── authentication/          # Azure SSO, OAuth2, user profiles
    ├── company/                 # Multi-tenant company structure
    ├── core/                    # Base models, SAP client, shared utils
    ├── payment_claim/           # Invoice claim workflows, MOA templates
    ├── project/                 # Projects, WBS, PurchaseOrders (SAP sync)
    ├── rvo/                     # RVO document workflows, MOA templates
    ├── permission/              # Fine-grained permission registry
    ├── storage/                 # File attachments (S3-backed)
    ├── notification/            # Email + FCM push notifications
    └── history/                 # Audit logging on model changes
```

---

## Apps Overview

### 1. `authentication`
- **Purpose:** User authentication and device management
- **Key Features:**
  - Azure Entra ID SSO (OAuth2/OIDC)
  - OTP support
  - User profile with profile image, signature, stamp uploads
  - Device tracking
- **Models:** `UserProfile`, `UserDevice`
- **Views:** Azure login/callback, session finalize

### 2. `company`
- **Purpose:** Multi-tenant company and user structure
- **Key Features:**
  - Company, Group (cost centers), CompanyUser management
  - Tool definitions (feature flags per company)
  - Company limits (user counts, storage quotas)
  - Notification settings per user
- **Models:** `Company`, `Group`, `CompanyUser`, `ToolDef`, `CompanyLimit`
- **User Roles:** Admin, Member, External (with permission levels)

### 3. `core`
- **Purpose:** Shared base models, SAP integration, utilities
- **Key Features:**
  - Base model hierarchy with soft deletes (django-safedelete)
  - SAP OData client wrapper
  - SAP sync Celery tasks
  - Shared REST helpers
- **Models:** `TimeStampedModel`, `BaseModel`, `BaseDocumentModel` (abstract)
- **Services:** `SAPClient` (OData wrapper)

### 4. `payment_claim`
- **Purpose:** Multi-step payment claim and invoice approval workflows
- **Key Features:**
  - Invoice claim templates and workflows
  - MOA (Memorandum of Agreement) templates
  - Park invoice management
  - Step-based approval process with assignees
  - Notification settings per step/tool/role
  - File attachments (park invoices, supporting docs)
- **Models:** `PaymentClaimTemplate`, `PaymentClaim`, `PaymentClaimStep`, `PaymentClaimAssignee`, `ParkInvoice`, `MOATemplate`, `Attachment`
- **Choices:** Request types, PDE categories, step statuses, notification triggers

### 5. `project`
- **Purpose:** Project management with SAP integration
- **Key Features:**
  - Projects with WBS (Work Breakdown Structure) hierarchies
  - Purchase Orders synced from SAP
  - Project images and metadata
  - Sync status tracking (Celery tasks)
- **Models:** `Project`, `WBS`, `PurchaseOrder`, `PurchaseOrderItem`, `PurchaseOrderService`
- **Services:** `ProjectSyncService`, `SAPClient` wrappers
- **SAP Fields:** Project definition, object numbers, company codes, responsible IDs

### 6. `rvo` (Request Verification Order)
- **Purpose:** RVO document workflows and MOA template selection
- **Key Features:**
  - RVO document creation and workflow steps
  - MOA template selection based on project attributes
  - MOA field injection (auto-fill from project/PR data)
  - SAP Purchase Request sync
  - Step cycles and response tracking
- **Models:** `RVO`, `RVOStep`, `RVOStepAssignee`, `RVOTemplate`, `MOARVOTemplate`, `SAPPurchaseRequest`, `SAPPurchaseRequestItem`
- **Services:** `MOASelector`, `MOAInjector`, `RVOService`, `SAPPRService`

### 7. `permission`
- **Purpose:** Fine-grained permission registry and resolver
- **Key Features:**
  - Role-based permission system
  - Permission registry for apps/models
  - Context-aware permission resolution
- **Models:** `Role`, `Permission`, `RolePermission`

### 8. `storage`
- **Purpose:** File attachment management
- **Key Features:**
  - AWS S3-backed file storage (django-storages)
  - File versioning
  - One-time download tokens
  - Storage quota tracking per company
- **Models:** `BaseFileAttachment`, `FileVersion`, `StorageUsage`

### 9. `notification`
- **Purpose:** Email and push notification system
- **Key Features:**
  - Async email sending via Celery
  - Firebase FCM push notifications
  - Email templates (Django templates)
  - Notification preferences per user
  - Overdue task notifications
- **Models:** `EmailTemplate`, `FirebaseDevice`, `NotificationLog`
- **Views:** Dev email preview endpoints

### 10. `history`
- **Purpose:** Audit logging on model changes
- **Key Features:**
  - Automatic history logging via mixins
  - Change tracking (old/new values)
  - User attribution (created_by, updated_by)
- **Models:** `HistoryLog`
- **Mixins:** `HistoryLogActionMixin` for ViewSets

---

## Architecture Patterns

### Model Hierarchy

```python
SafeDeleteModel (django-safedelete, SOFT_DELETE_CASCADE)
  └── TimeStampedModel
        ├── created_at, updated_at
        └── BaseModel
              ├── created_by, updated_by (FK → User)
              └── BaseDocumentModel (optional)
                    ├── title, code (unique), description
                    └── [domain models]
```

### Layering (Strict)

```
Model → Service → Serializer → View
```

- **Services** in `apis/<app>/services/` hold all business logic
- Services take an `actor` (User) on init: `PaymentClaimService(actor=request.user).create(...)`
- **Views** orchestrate only; no heavy logic in views or serializers
- **Enums** go in `choices.py` (use `TextChoices`)
- **Complex data transforms** go in `mappers.py`
- **Decoupled events** use `signals.py` + `receivers.py`

### API Versioning

- Views go in `views/v1/`, `views/v2/`, etc.
- Document all endpoints with `@extend_schema` (drf-spectacular)
- URL structure: `/api/<app>/v1/<resource>/`

### Soft Deletes

- All `BaseModel` subclasses use `SOFT_DELETE_CASCADE`
- Default manager (`Model.objects`) excludes deleted rows
- Use `Model.all_objects` only when deleted rows are explicitly needed
- Custom filters/joins must include `deleted__isnull=True` where appropriate

### Query Optimization

- Use `select_related` for FK/OneToOne
- Use `prefetch_related` for M2M and reverse FK
- Use `CreatedUpdatedByOptimizationMixin` in ViewSets with heavy `created_by`/`updated_by` usage

---

## External Integrations

| Integration | Type | Purpose |
|-------------|------|---------|
| **SAP ERP** | OData APIs | Project/WBS/PO sync, Purchase Requests |
| **Azure Entra ID** | OAuth2/OIDC | Single Sign-On (SSO) |
| **AWS S3** | boto3 | File storage backend |
| **Firebase** | FCM API | Push notifications to mobile/web |
| **SMTP** | Email backend | Transactional emails |

---

## Commands (Development)

### Setup

```bash
uv sync                                    # Install/sync dependencies
cp .env.example .env                       # Configure environment (first time)
docker-compose up -d                       # Start PostgreSQL 15 + Redis 7
uv run python manage.py migrate            # Apply migrations
uv run python manage.py runserver          # Dev server on :8000
```

### Lint and Format

```bash
uv run ruff check --fix .                  # Lint + auto-fix
uv run ruff format .                       # Format
```

### Testing

```bash
# Django test runner
uv run python manage.py tests                                    # Full suite
uv run python manage.py tests apis.company.tests                 # Single app
uv run python manage.py tests apis.company.tests.test_services.MyTests.test_method  # Single method

# Pytest (preferred for new tests)
uv run pytest -v                                                 # Full suite
uv run pytest -v -k "Unit"                                       # Unit only (fast, no DB)
uv run pytest -v -k "Integration"                                # Integration only (real DB)
uv run pytest apis/project/tests/services/sap/test_project_sync_service.py -v  # Single file
```

### Other

```bash
uv run python manage.py makemigrations
uv run python manage.py shell
uv run python manage.py createsuperuser
uv run python manage.py all_seeds              # Seed all fixtures (dev only)
uv run celery -A paystation_backend worker -l info
uv run celery -A paystation_backend beat -l info
uv pip compile pyproject.toml -o requirements.txt   # Update requirements.txt
```

---

## Agent Workspace (.claude/)

Session-scoped plans and task tracking:

```
.claude/
  plan/          # Active plan for current task (read at session start)
  done/
    <feature>/   # Completed plans, archived by feature name
  docs/          # Context briefing documents (background info)
  
.claude/projects/-home-tanakorn-Documents-workspace-paystation-backend/memory/
  MEMORY.md      # Persistent learnings across sessions
```

**Workflow:**
1. At session start, check `.claude/plan/` for active task/implementation plan
2. On completion, move `.claude/plan/` contents into `.claude/done/<feature-name>/`
3. For context briefing (background info, domain knowledge), write to `.claude/docs/`

---

## Code Style

- **Line length:** 120 chars max
- **Quote style:** Double quotes
- **Python target:** 3.13
- **Import order:** future → stdlib → third-party → first-party (`paystation_backend`, `apis`) → local
- **Type hints:** Explicit on public functions/methods (no bare `Any`)
- **Test naming:** `test_*.py`, classes `Test*Unit` (no DB) / `Test*Integration` (DB via `@pytest.mark.django_db`)

### Ruff Lint Families

```
E, F, I, UP, B, DJ, N, Q, PT, S
```

Ignored globally: `D` (docstrings), `ANN` (annotations), `S101` (assert in tests)

---

## Key Files

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Comprehensive guide for AI agents (commands, architecture, patterns) |
| `pyproject.toml` | Dependencies, Ruff config, pytest settings |
| `schema.yml` | OpenAPI 3.0 spec (auto-generated) |
| `docker-compose.yml` | Local dev services (PostgreSQL, Redis) |
| `.venv/` | Virtual environment (uv-managed) |

---

## Notes

- **Multi-tenant:** Company-based isolation with `CompanyContext` in API auth
- **Audit Trail:** All models inherit `created_by`, `updated_by`, `created_at`, `updated_at`
- **Soft Deletes:** Default queries exclude deleted rows (use `all_objects` to include)
- **Async Everything:** Email, SAP sync, notifications all run via Celery
- **Realtime:** Django Channels for WebSocket notifications
- **API-First:** Full OpenAPI docs at `/api/docs/` (Swagger UI) or `/api/redoc/`
