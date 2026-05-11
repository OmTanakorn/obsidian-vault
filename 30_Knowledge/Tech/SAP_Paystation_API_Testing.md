---
type: knowledge
topic: SAP Paystation API Testing
tags: [sap, api, testing, paystation, pr, po, release]
publish: true
last_updated: 2026-04-16
---

# SAP Paystation API Testing Guide

> **Scope:** PR/PO Create/Change and Release operations testing  
> **Source:** `API Information V1.csv` + Bruno collections  
> **Last Updated:** 2026-04-16

## Quick Summary

**Target Endpoints (4):**
1. **PR Create/Change** – `/prSet` (POST/PUT)
2. **PO Create/Change** – `/poSet` (POST/PUT)
3. **PR Release** – `/prreleaseSet` (POST)
4. **PO Release** – `/poreleaseSet` (POST)

**Credentials:**
- **DEV:** `WEBSERV:QdFSAF@oytvn!Q1^@83` (client=200)
- **QAS:** `PRPOCON:****` (client=610) – password in `.env`

**CSRF Token Required:** GET `/gettoken` with `X-CSRF-Token: Fetch` header

**⚠️ Important Notes:**
- **Token expiration:** CSRF tokens may expire unexpectedly. Always obtain fresh token before each test session.
- **Network connectivity:** DEV endpoint (`sapdev`) may be internal-only; QAS endpoint may require VPN/proxy.
- **Environment variables:** For QAS testing, set `SAP_USERNAME` and `SAP_PASSWORD` from `.env` file.

**Testing Checklist:**
- [ ] Choose environment (DEV/QAS)
- [ ] Set environment variables if using QAS
- [ ] Obtain fresh CSRF token
- [ ] Test PR Create with sample payload
- [ ] Test PO Create with sample payload
- [ ] Test PR Release (if PR number available)
- [ ] Test PO Release (if PO number available)

---

## 0. Environment Selection

Two environments available:

### **DEV (sapdev)** — CSV credentials
- **Base URL:** `https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/`
- **Username:** `WEBSERV`
- **Password:** `QdFSAF@oytvn!Q1^@83`
- **SAP Client:** `200`
- **Status:** Connection currently timing out (test required tomorrow)

### **QAS (apricot)** — Bruno collection
- **Base URL:** `https://awc-apricot-dev.assetworldcorp-th.com`
- **Credentials:** Via environment variables (`SAP_USERNAME`, `SAP_PASSWORD`)
- **SAP Client:** `610` (default)
- **Bruno Path:** `/home/tanakorn/Documents/docs/AWC Sap Collection/`
- **Status:** Requires valid environment variables

**Recommendation:** Test on DEV first (credentials known). If unavailable, use QAS with proper credentials.

## 1. Authentication & CSRF Token Flow

### Credentials
- **Username:** `WEBSERV`
- **Password:** `QdFSAF@oytvn!Q1^@83`
- **SAP Client:** `200`

### CSRF Token Acquisition
**Step 1 — Get Token:**
```bash
curl -X GET \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/gettoken?sap-client=200" \
  -H "X-CSRF-Token: Fetch" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83"
```

**Step 2 — Extract Token:**
Token is returned in response headers as `x-csrf-token`. Save for subsequent POST/PUT requests.

**Step 3 — Use Token:**
Include in request headers:
```
X-CSRF-Token: <token-from-step2>
```

---

## 2. Endpoints Overview

| # | Endpoint | Method | Type | Description |
|---|----------|--------|------|-------------|
| 14-15 | `/prSet` | POST, UPDATE | MM | PR Create (POST) and Change (UPDATE) |
| 16-17 | `/poSet` | POST, UPDATE | MM | PO Create (POST) and Change (UPDATE) |
| 23 | `/prreleaseSet` | POST | MM | PR Release |
| 24 | `/poreleaseSet` | POST | MM | PO Release |

> **Note:** According to CSV, PR Create/Change share same endpoint (`/prSet`), same for PO (`/poSet`).

---

## 3. Detailed Testing Instructions

### 3.1 PR Create/Change (`/prSet`)

**Base URL:** `https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/prSet`

**Create (POST):**
```bash
curl -X POST \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/prSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "field1": "value1",
    "field2": "value2"
  }'
```

**Change (UPDATE):**
```bash
curl -X UPDATE \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/prSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "field1": "new_value1",
    "field2": "new_value2"
  }'
```

**Testing Checklist:**
- [ ] CSRF token obtained successfully
- [ ] POST request returns 201 Created or 200 OK
- [ ] Response contains PR number/reference
- [ ] UPDATE request modifies existing PR
- [ ] Error handling for invalid data

### 3.2 PO Create/Change (`/poSet`)

**Base URL:** `https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/poSet`

**Create (POST):**
```bash
curl -X POST \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/poSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "po_data": "sample"
  }'
```

**Change (UPDATE):**
```bash
curl -X UPDATE \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/poSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "po_data": "updated"
  }'
```

**Testing Checklist:**
- [ ] CSRF token valid
- [ ] POST creates new PO
- [ ] Response includes PO number
- [ ] UPDATE modifies PO
- [ ] Validate business rules (e.g., vendor, project mapping)

### 3.3 PR Release (`/prreleaseSet`)

**Base URL:** `https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/prreleaseSet`

**Release (POST):**
```bash
curl -X POST \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/prreleaseSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "pr_number": "1234567890"
  }'
```

**Testing Checklist:**
- [ ] Valid PR number provided
- [ ] Release operation succeeds
- [ ] Status changes to "Released"
- [ ] Error if PR already released

### 3.4 PO Release (`/poreleaseSet`)

**Base URL:** `https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/poreleaseSet`

**Release (POST):**
```bash
curl -X POST \
  "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/poreleaseSet" \
  -H "X-CSRF-Token: <token>" \
  -H "Content-Type: application/json" \
  -u "WEBSERV:QdFSAF@oytvn!Q1^@83" \
  -d '{
    "po_number": "9876543210"
  }'
```

**Testing Checklist:**
- [ ] Valid PO number provided
- [ ] Release operation completes
- [ ] PO status updated
- [ ] Integration with downstream systems

---

## 4. Actual Request Structures (from Bruno)

Based on existing Bruno collection (`/home/tanakorn/Documents/docs/AWC Sap Collection/`).

### 4.1 PR Create (QAS Environment)
**URL:** `{{url}}/PR/PayStation/qas/prSet?sap-client={{sap_client}}`  
**Method:** POST  
**Headers:** `X-CSRF-Token: {{csrf_token}}`  
**Payload (example):**
```json
{
  "doctype": "ZRVA",
  "option": "1",
  "headernote": "",
  "rvopreprecby": "",
  "rvoapprejby": "",
  "prdetails": [
    {
      "itemno": "0010",
      "accasscat": "P",
      "itemcat": "",
      "material": "",
      "shorttext": "Just stop your crying",
      "deliverydate": "2026-04-16T00:00:00",
      "purgrp": "123",
      "requisitioner": "developer",
      "plant": "1102",
      "trackingno": "LMCM-CON",
      "matgrp": "32-008",
      "quantity": "1.000",
      "unit": "AU",
      "valuationprice": "14973.00",
      "priceunit": "1",
      "currency": "THB",
      "refcontract": "4100001996",
      "refitem": "10",
      "reason1": "",
      "reason2": "",
      "reason3": "",
      "reason4": "",
      "reason5": "",
      "reason6": "X",
      "reason7": "",
      "reason8": "",
      "detailspecify": "",
      "deleteind": "",
      "itemtext": "",
      "itemnote": "",
      "deliverytext": "",
      "matpotext": "",
      "claimref": "",
      "claimdate": "",
      "claimamt": "",
      "qsassenssment": "",
      "agreeamt": "",
      "remark": "",
      "praccs": [
        {
          "serialno": "1",
          "glacc": "5908000100",
          "wbs": "C.6053.00.011.1.14.04",
          "coarea": "",
          "network": "",
          "operationno": "",
          "costcenter": "",
          "assetno": "",
          "assetsub": "",
          "order": "",
          "recipient": "",
          "unloadingpoint": ""
        }
      ],
      "prservices": []
    }
  ]
}
```

### 4.2 PO Create (QAS Environment)
**URL:** `{{url}}/PO/PayStation/qas/poSet?sap-client={{sap_client}}`  
**Method:** POST  
**Headers:** `X-CSRF-Token: {{csrf_token}}`, `Content-Type: application/json`  
**Payload:**
```json
{
  "PoNumber": "",
  "PoType": "41",
  "CompanyCode": "{{target_company_single}}",
  "Vendor": "1000001",
  "PurchaseOrg": "1000",
  "PurchaseGroup": "001",
  "Items": [
    {
      "ItemNo": "10",
      "ShortText": "Construction Material",
      "Quantity": "10",
      "Unit": "EA",
      "NetPrice": "1000.00",
      "Plant": "1000",
      "GlAccount": "51001010",
      "CostCenter": "CC001",
      "WbsElement": "C.6003.24.100"
    }
  ]
}
```

### 4.3 PO Release (QAS Environment)
**URL:** `{{url}}/PO/PayStation/qas/poreleaseSet?sap-client={{sap_client}}`  
**Method:** POST  
**Headers:** `X-CSRF-Token: {{csrf_token}}`  
**Payload:** (Check Bruno file for actual structure)

### 4.4 Missing Endpoints in Bruno
- **PR Change:** File exists but payload undefined
- **PR Release:** No Bruno file found (`prreleaseSet`)
- **PO Change:** File exists but payload placeholder only

**Note:** QAS environment uses different URL structure and requires `SAP_USERNAME`/`SAP_PASSWORD` environment variables.

---

## 5. Python Testing Script (Optional)

```python
import requests
from requests.auth import HTTPBasicAuth

BASE_URL = "https://sapdev.assetworldcorp-th.com:8443/sap/opu/odata/sap/zgw_paystation_srv/"
AUTH = HTTPBasicAuth("WEBSERV", "QdFSAF@oytvn!Q1^@83")

def get_csrf_token():
    """Get CSRF token for POST operations."""
    response = requests.get(
        f"{BASE_URL}gettoken?sap-client=200",
        headers={"X-CSRF-Token": "Fetch"},
        auth=AUTH,
        verify=False  # SSL verification disabled for dev
    )
    return response.headers.get("x-csrf-token")

def test_pr_create(token, data):
    """Test PR Create endpoint."""
    response = requests.post(
        f"{BASE_URL}prSet",
        headers={
            "X-CSRF-Token": token,
            "Content-Type": "application/json"
        },
        auth=AUTH,
        json=data,
        verify=False
    )
    return response

# Add similar functions for other endpoints
```

---

## 6. Common Issues & Troubleshooting

| Issue | Possible Cause | Solution |
|-------|----------------|----------|
| 401 Unauthorized | Invalid credentials | Verify username/password |
| 403 Forbidden | Missing/invalid CSRF token | Re-fetch token with "Fetch" header |
| 405 Method Not Allowed | Wrong HTTP method | Check CSV for correct method (POST/UPDATE) |
| 500 Internal Server Error | Invalid request data | Check payload format, required fields |

---

## 7. References

- **Source File:** `API Information V1.csv` (local: `/home/tanakorn/Documents/API Information V1.csv`)
- **Related Notes:** [[RVO_Model_CRUD_Summary]] (for PR/PO context)
- **SAP Documentation:** OData service documentation (if available)
- **Bruno Collection:** `/home/tanakorn/Documents/docs/AWC Sap Collection/`

---

## 8. Next Steps

1. **Environment Setup:**
   - Determine which environment to test (DEV or QAS)
   - For DEV: verify connectivity to `sapdev` server
   - For QAS: set `SAP_USERNAME` and `SAP_PASSWORD` environment variables

2. **CSRF Token:**
   - Obtain token via GET request with `X-CSRF-Token: Fetch` header
   - Store token for subsequent requests

3. **Endpoint Testing:**
   - Start with simple GET endpoints (e.g., `wbsSet`) to verify connectivity
   - Test PR Create (`prSet`) with sample payload
   - Test PO Create (`poSet`) with sample payload
   - Test release operations (`prreleaseSet`, `poreleaseSet`)

4. **Documentation:**
   - Record actual request/response formats
   - Update this note with successful examples
   - Note any deviations from expected behavior