---
type: project
status: active
stack:
  - SAP
  - Django
  - React
---
# 🚀 API Reference: Create RVO (Normal & Urgent)
API สำหรับการสร้างเอกสาร RVO ใหม่ พร้อมเชื่อมต่อและขอเลข PR จากระบบ SAP ทันที (Synchronous) โดยแบ่ง Endpoint ตามประเภทของงาน เพื่อลดความซับซ้อนของการส่งข้อมูลจากหน้าบ้าน
## 📍 Endpoints
- **Normal RVO:** `POST /apis/normal-rvos/`
- **Urgent RVO:** `POST /apis/urgent-rvos/`

## 🔒 Headers (Required)

| **Key**         | **Value**        | **Description**                                  |
| --------------- | ---------------- | ------------------------------------------------ |
| `Authorization` | `Bearer <token>` | Access Token ของผู้ใช้งาน                        |
| `X-Project-ID`  | `Integer`        | ID ของโปรเจกต์ปัจจุบัน (นำมาใช้แทนการส่งใน Body) |
## 📥 Request Payload (Body)

หน้าบ้านส่งเฉพาะข้อมูลที่ User กรอกบนฟอร์ม โดยจัด Grouping ไว้ 3 ก้อนหลัก ระบบหลังบ้านจะจัดการเติมค่า `rvo_type` และ `template_id` ให้อัตโนมัติ
เพิ่มเติมดูได้ใน docs
```JSON
{
  "rvo_data": {
    "title": "ลดสเปกกระเบื้องห้องน้ำ",
    "purchase_order_id": 99 ,
    "purchase_order_item_id": 199
  },
  
  "pr_data": {
    "pr_type": "omission",
    "acct_assignment": "P",
    "quantity": 1,
    "unit": "AU",
    "request_amount": 1000000.00,
    "currency": "THB",
    "vendor": 990,
    "tracking_number": "LMCM-CON",
    "material_group": "41-01-122",
    "plant": "1093",
    "purch_group": "123",
    "date_of_rvo": "2026-04-04",
    "variation_data": {
	    "reference_contract": "4100001996",  
	    "reference_items": "10", 
	    "reasons": [
	      "site_condition",
	      "cost_optimization"
	    ],
	    "reasons_other": "No Money"
	},
    "texts": {
		"item_text": "...",
		"item_note": "...",
		"delivery_text": "...",
		"mat_po_text": "...",
		"claim_ref": "...",
		"claim_date": "...",
		"claim_amt": "...",
		"qs_assessment": "...",
		"agree_amt": "...",
		"remark": "..."
		}
	},
"attachments": {file} ยังไม่ทำ
}
```
- reference_contract เอาเลข PO มาเติม
- reference_items เอา Poitem มาเติม
- reasons
### 📝 รายละเอียดฟิลด์สำคัญ (Field Notes)
- `variation_data.reasons`: รับเป็น Array ของ String เท่านั้น (อ้างอิงค่า Enum จากหน้าบ้าน)
- หากมีค่าที่ไม่อยู่ใน 8 เหตุผลหลักของ SAP (เช่น `cost_optimization`, `schedule_phasing`) **Backend จะทำการติ๊ก Other (reason8) และโยนข้อความเข้าฟิลด์ `detail specify` ของ SAP ให้อัตโนมัติ**

## 📤 Response (201 Created)
ระบบจะตอบกลับเป็นข้อมูล RVO ฉบับสมบูรณ์ (ที่ผ่านการจัด Grouping และ Reverse Mapping กลับมาเป็น Array แล้ว) **พร้อมแสดงเลข PR จาก SAP ทันที**
```JSON
{
  "id": 123,
  "rvo_number": "RVO-2026-0001",
  "status": "in_progress",
  "rvo_type": "normal",
  "created_at": "2026-03-26T10:00:00Z",
  "created_by": {
    "id": 5,
    "name": "Nitinat M.",
    "role": "Project Manager"
  },
  
  "rvo_data": {
    "title": "ลดสเปกกระเบื้องห้องน้ำ",
    "description": "เปลี่ยนสเปกจากเกรด A เป็นเกรด B ตามคำขอของ Owner",
    "purchase_order": {
       "id": 99,
       "po_number": "PO-OLD-001",
       "vendor_name": "บริษัท ABC"
    }
  },
  
  "pr_data": {
    "pr_type": "omission",
    "request_amount": 1000000.00,
    "date_of_rvo": "2026-04-04",
    "purch_group": "A01",
    "po_number": "PO-2026-0001",
    "header_note": "สร้าง PR สำหรับงานลดสเปกโครงการ A",
    "item_text": "รายละเอียดรายการที่ 1",
    "remark": "หมายเหตุระดับรายการ",
    "qs_assessment": "QS ตรวจสอบราคาตลาดแล้วเหมาะสม",
    
    "sap_pr_number": "7100022328", 
    "sap_status": "success" 
  },
  
  "variation_data": {
    "reference_contract": "CTR-2026-001",
    "reference_items": "Item 1-5",
    "reasons": [
      "site_condition",
      "cost_optimization"
    ]
  },
  
  "workflow": {
    "current_step_name": "QS Review",
    "is_my_turn": false
  }
}
```

```typescript

```
## ⚠️ Error Handling (Status Codes)
ระบบจัดการ Exception และ Response ให้อัตโนมัติ ดังนี้:
- **`400 Bad Request`**: กรณี Frontend ส่ง Payload ผิด Type หรือฟิลด์ที่ Required ขาดหายไป (DRF Validation)
- **`400 Bad Request` (Custom)**: กรณีไม่พบ Template ที่ใช้งานได้ใน Project นั้น (ดักจับจาก `ValueError` ใน Service)
- **`201 Created` (พร้อม Warning)**: กรณีสร้างเอกสารลง Database สำเร็จ **แต่ระบบ SAP ล่ม หรือ SAP ตีกลับ (Error)** * `pr_data.sap_status` จะมีค่าเป็น `"failed"`
    - `pr_data.sap_pr_number` จะเป็นค่าว่าง `""`
    - (ข้อแนะนำหน้าบ้าน: ควรแสดง UI Alert ให้ User ทราบว่าสร้างเอกสารสำเร็จ แต่ยังไม่ได้เลข PR ให้กดปุ่ม `[Sync SAP]` อีกครั้งในภายหลัง)
