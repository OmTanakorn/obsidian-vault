---
type: knowledge
topic: RVO Model & CRUD
tags: [rvo, django, model, crud, sap, architecture]
publish: true
last_updated: 2026-03-05
---

# RVO — Model & Architecture Summary

> **Scope:** Full Model Design + CRUD + Relationship Structure  
> **Updated:** 2026-03-05

---

## 1. Entity Relationship Overview

### 1.1 Core & External Relations
- **External Entities:** `Project`, `PurchaseOrder`, `PurchaseOrderItem`, `User`
- **RVO Core:** `RVO`, `RVOStep`, `RVOStepAssignee`
- **Cycle Management:** `RVOStepCycle`, `RVOStepResponse`
- **Template System:** `RVOTemplate`, `RVOStepTemplate`, `RVOAssigneeTemplate`
- **MOA (Manual of Authority):** `MOARVOTemplate`, `MOARVOStepTemplate`, `MOARVOAssigneeTemplate`
- **SAP Integration:** `SAPPurchaseRequest`, `SAPPRVariationWork`, `SAPPRVariationWorkReason`, `SAPPRSyncLog`

---

## 2. Detailed Model Schema

### 2.1 RVO Core
```python
class RVO(BaseModel):
    project              = ForeignKey("project.Project")
    template             = ForeignKey("rvo.RVOTemplate", null=True)
    purchase_order       = ForeignKey("project.PurchaseOrder", null=True)
    purchase_order_item  = ForeignKey("project.PurchaseOrderItem", null=True)
    rvo_type             = CharField(choices=RVOType)
    status               = CharField(choices=RVOStatus, default=DRAFT)
    title                = CharField(max_length=255)
    description          = TextField(blank=True)
    code                 = CharField(max_length=50, blank=True) # RVO Number
    current_step         = PositiveIntegerField(null=True)

class RVOStep(BaseModel):
    rvo                  = ForeignKey(RVO, related_name="steps")
    parent_step          = ForeignKey("self", null=True)
    moa_template         = ForeignKey("rvo.MOARVOTemplate", null=True)
    order                = PositiveIntegerField()
    sub_order            = PositiveIntegerField(default=0)
    response_type        = CharField()
    step_label           = CharField()
    status               = CharField()
    option_type          = CharField()
    duration_days        = PositiveIntegerField()
    push_back_to         = PositiveIntegerField(null=True)
    is_assign_to_vendor  = BooleanField(default=False)
    attachment_type      = CharField()
    current_cycle        = PositiveIntegerField(default=1)
    started_at           = DateTimeField(null=True)
    due_date             = DateTimeField(null=True)
    completed_at         = DateTimeField(null=True)
```

### 2.2 SAP Integration
```python
class SAPPurchaseRequest(BaseModel):
    rvo       = ForeignKey("rvo.RVO", related_name="purchase_requests")
    status    = CharField(choices=SAPPRStatus, default=PENDING)
    is_active = BooleanField(default=True)
    # PR Fields
    pr_type                   = CharField(choices=PRType)
    subject                   = CharField(max_length=255)
    acct_assignment_category  = CharField(default="P")
    pde_category              = CharField(blank=True)
    short_text                = CharField(blank=True)
    quantity                  = DecimalField(default=1)
    unit                      = CharField(default="AU")
    request_amount            = DecimalField(null=True)
    tracking_number           = CharField(blank=True)
    vendor                    = CharField(blank=True)
    material_group            = CharField(blank=True)
    plant                     = CharField(blank=True)
    purch_group               = CharField(blank=True)
    date_of_rvo               = DateField(null=True)
    text                      = TextField(blank=True)
    wbs                       = CharField(blank=True)
    # Sync Status
    sap_pr_number             = CharField(blank=True)
    sap_released_at           = DateTimeField(null=True)
```

---

## 3. Workflow & Logic

### 3.1 Cycle Management
Each `RVOStep` can have multiple `RVOStepCycle`.
- A cycle is created when a step is "Pushed Back" or needs re-review.
- `RVOStepResponse` tracks individual user actions within a cycle.

### 3.2 MOA Step Injection
- Steps can be dynamically linked to an `MOARVOTemplate` based on the `request_amount`.
- MOA templates define specific approval flows for high-value RVOs.

---

## 4. Relations Summary (Logic Map)

- **RVO -> Template:** `RVO` follows a `RVOTemplate`.
- **RVO -> Project:** Each `RVO` belongs to exactly one `Project`.
- **RVO -> SAP:** Each `RVO` has one active `SAPPurchaseRequest`.
- **Step -> Assignee:** Each `RVOStep` has multiple `User` assignees via `RVOStepAssignee`.
- **Response -> Assignee:** Each action is tied to an assignee and a specific cycle.

---

## 5. Metadata
- **RVO Number Format:** `RVO-{Year}-{PK:04d}`
- **Constraint:** Only one `SAPPurchaseRequest` per `RVO` can be `is_active=True`.
