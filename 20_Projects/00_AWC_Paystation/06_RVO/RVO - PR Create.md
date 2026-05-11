#### RVO and SAP PR Information
___
#### Reason Mapping Strategy

To ensure data consistency between the user interface, backend storage, and SAP integration, the following mapping is used. Note that multiple user-facing reasons may map to a single SAP boolean field (e.g., `reason_design_refinement`).

| No  | User Display Label                        | Enum Key (`ReasonChoice`) | SAP Integration Field      |
| :-- | :---------------------------------------- | :------------------------ | :------------------------- |
| 1   | Design-Related Changes/Faulty             | `DESIGN_FAULTY`           | `reason_faulty_design`     |
| 2   | Authorities Requirement                   | `AUTHORITIES_REQUIRE`     | `reason_authorities_req`   |
| 3   | Schedule & Phasing Changes                | `SCHEDULE_PHASING`        | `reason_other` (Fallback)  |
| 4   | Operator/Customer Requirement             | `OPERATOR_CUSTOMER`       | `reason_customer_req`      |
| 5   | Material & Specification Changes          | `MATERIAL_SPEC`           | `reason_design_refinement` |
| 6   | Construction Method Adjustment            | `CONSTRUCTION_METHOD`     | `reason_other` (Fallback)  |
| 7   | Cost Optimization / Value Engineering     | `COST_OPTIMIZATION`       | `reason_other` (Fallback)  |
| 8   | Scope & Requirement Changes               | `SCOPE_REQUIRE`           | `reason_missing_scope`     |
| 9   | AWC's Requirement                         | `AWC_REQUIRE`             | `reason_tcc_requirement`   |
| 10  | Site Condition & Construction Constraints | `SITE_CONDITION`          | `reason_site_condition`    |
| 11  | Others (Specify)                          | `OTHERS`                  | `reason_other`             |

---

### Implementation Details

#### 1. Backend (Django Enum)
```python
from django.db import models

class ReasonChoice(models.TextChoices):
    # Format: VARIABLE_NAME = "db_value", "Display Name"
    DESIGN_FAULTY = "design_faulty", "Design-Related Changes/Faulty"
    AUTHORITIES_REQUIRE = "authorities_require", "Authorities Requirement"
    SCHEDULE_PHASING = "schedule_phasing", "Schedule & Phasing Changes"
    OPERATOR_CUSTOMER = "operator_customer", "Operator/Customer Requirement"
    MATERIAL_SPEC = "material_spec", "Material & Specification Changes"
    CONSTRUCTION_METHOD = "construction_method", "Construction Method Adjustment"
    COST_OPTIMIZATION = "cost_optimization", "Cost Optimization / Value Engineering"
    SCOPE_REQUIRE = "scope_require", "Scope & Requirement Changes"
    AWC_REQUIRE = "awc_require", "AWC's Requirement"
    SITE_CONDITION = "site_condition", "Site Condition & Construction Constraints"
    OTHERS = "others", "Others (Specify)"
```

#### 2. Frontend (TypeScript)

##### Enum Definition
```typescript
export enum ReasonChoice {
  DESIGN_FAULTY = 'design_faulty',
  AUTHORITIES_REQUIRE = 'authorities_require',
  SCHEDULE_PHASING = 'schedule_phasing',
  OPERATOR_CUSTOMER = 'operator_customer',
  MATERIAL_SPEC = 'material_spec',
  CONSTRUCTION_METHOD = 'construction_method',
  COST_OPTIMIZATION = 'cost_optimization',
  SCOPE_REQUIRE = 'scope_require',
  AWC_REQUIRE = 'awc_require',
  SITE_CONDITION = 'site_condition',
  OTHERS = 'others',
}
```

##### Options for Select Inputs
```typescript
export const ReasonChoiceOptions = [
  { value: ReasonChoice.DESIGN_FAULTY, label: 'Design-Related Changes/Faulty' },
  { value: ReasonChoice.AUTHORITIES_REQUIRE, label: 'Authorities Requirement' },
  { value: ReasonChoice.SCHEDULE_PHASING, label: 'Schedule & Phasing Changes' },
  { value: ReasonChoice.OPERATOR_CUSTOMER, label: 'Operator/Customer Requirement' },
  { value: ReasonChoice.MATERIAL_SPEC, label: 'Material & Specification Changes' },
  { value: ReasonChoice.CONSTRUCTION_METHOD, label: 'Construction Method Adjustment' },
  { value: ReasonChoice.COST_OPTIMIZATION, label: 'Cost Optimization / Value Engineering' },
  { value: ReasonChoice.SCOPE_REQUIRE, label: 'Scope & Requirement Changes' },
  { value: ReasonChoice.AWC_REQUIRE, label: "AWC's Requirement" },
  { value: ReasonChoice.SITE_CONDITION, label: 'Site Condition & Construction Constraints' },
  { value: ReasonChoice.OTHERS, label: 'Others (Specify)' },
];
```

##### Label Mapping (Read-only)
```typescript
export const ReasonChoiceLabels: Record<ReasonChoice, string> = {
  [ReasonChoice.DESIGN_FAULTY]: 'Design-Related Changes/Faulty',
  [ReasonChoice.AUTHORITIES_REQUIRE]: 'Authorities Requirement',
  [ReasonChoice.SCHEDULE_PHASING]: 'Schedule & Phasing Changes',
  [ReasonChoice.OPERATOR_CUSTOMER]: 'Operator/Customer Requirement',
  [ReasonChoice.MATERIAL_SPEC]: 'Material & Specification Changes',
  [ReasonChoice.CONSTRUCTION_METHOD]: 'Construction Method Adjustment',
  [ReasonChoice.COST_OPTIMIZATION]: 'Cost Optimization / Value Engineering',
  [ReasonChoice.SCOPE_REQUIRE]: 'Scope & Requirement Changes',
  [ReasonChoice.AWC_REQUIRE]: "AWC's Requirement",
  [ReasonChoice.SITE_CONDITION]: 'Site Condition & Construction Constraints',
  [ReasonChoice.OTHERS]: 'Others (Specify)',
};
```

---

### 3. SAP Integration (Payload structure)

The backend must map the single `ReasonChoice` selected by the user into the corresponding boolean flag for SAP.

```json
{
    "reason_design_refinement": false,
    "reason_tcc_requirement": false,
    "reason_site_condition": false,
    "reason_missing_scope": false,
    "reason_authorities_req": false,
    "reason_faulty_design": false,
    "reason_customer_req": false,
    "reason_other": false
}
```

**SAP Field Mapping Logic:**
- `reason_faulty_design` ⮕ `DESIGN_FAULTY`
- `reason_authorities_req` ⮕ `AUTHORITIES_REQUIRE`
- `reason_customer_req` ⮕ `OPERATOR_CUSTOMER`
- `reason_design_refinement` ⮕ `MATERIAL_SPEC`
- `reason_missing_scope` ⮕ `SCOPE_REQUIRE`
- `reason_tcc_requirement` ⮕ `AWC_REQUIRE`
- `reason_site_condition` ⮕ `SITE_CONDITION`
- `reason_other` ⮕ (`OTHERS` OR `SCHEDULE_PHASING` OR `CONSTRUCTION_METHOD` OR `COST_OPTIMIZATION`)

---

### 📄 Current PR Request Data
**PR Type:** addition | **Item No:** stri | **Material:** string

#### Details
- **Account Assignment Category:** string
- **Item Category:** s
- **Short Text:** string
- **Delivery Date:** 2026-03-23
- **Purchase Group:** str
- **Requisitioner:** string
- **Plant:** stri
- **SLoc:** stri
- **Tracking No:** string
- **Material Group:** string
- **Quantity:** -410,876,979
- **Unit:** str
- **Valuation Price:** -57,233.00
- **Price Unit:** -98.00
- **Currency:** strin
- **Request Date:** 2026-03-23
- **Ref Contract:** string
- **Ref Item:** string
- **Line No:** string

#### Reasons (Flags)
- **Design Refinement:** ✅
- **TCC Requirement:** ✅
- **Site Condition:** ✅
- **Missing Scope:** ✅
- **Authorities Req:** ✅
- **Faulty Design:** ✅
- **Customer Req:** ✅
- **Other:** ✅

#### 💳 Accounts
| GL Acc | WBS | CO Area | Network | Op No | Cost Center | Asset No | Asset Sub | Order | Recipient | Unloading Point |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| string | string | string | string | string | string | string | stri | string | string | string |

#### 🛠️ Services
| Pkg No | Line In | Line No | Del Ind | Act No | Short Text | Quantity | Unit | Net Value | Gross Price | Over Ful | Unlimit |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| string | string | string | s | string | string | -3675. | str | 995,590,357.1 | -4 | | s |


