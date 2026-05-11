# รายการทดสอบระบบ Audit Log (History Log)

เอกสารนี้รวบรวม API Endpoints และ Scenario ที่ต้องทดสอบเพื่อให้มั่นใจว่าระบบบันทึกประวัติการใช้งาน (Audit Log) ทำงานได้อย่างถูกต้องและครอบคลุมทุก Module

## 1. Module: Company Users (สมาชิกในบริษัท)
**Base Endpoint:** `/api/v1/company-users/`

| ลำดับ | การทำงาน (Action) | Method | Endpoint | ผลที่คาดหวัง (Expected Log Event) |
| :--- | :--- | :--- | :--- | :--- |
| 1.1 | เชิญสมาชิกใหม่ | `POST` | `/` | บันทึก `MEMBER_INVITED` (แสดงชื่อ User และ Company) |
| 1.2 | แก้ไขข้อมูลสมาชิก | `PATCH` | `/{id}/` | บันทึก `MEMBER_UPDATED` |
| 1.3 | ลบสมาชิกออกจากบริษัท | `DELETE` | `/{id}/` | บันทึก `MEMBER_REMOVED` |
| 1.4 | เปิดใช้งาน (Activate) | `POST` | `/{id}/active/` | บันทึก `MEMBER_ACTIVATED` |
| 1.5 | ปิดใช้งาน (Deactivate) | `POST` | `/{id}/deactivate/` | บันทึก `MEMBER_DEACTIVATED` |
| 1.6 | เปิดใช้งานแบบกลุ่ม (Bulk) | `POST` | `/bulk-active/` | บันทึก `BULK_ACTIVATED` (แสดงจำนวนสมาชิกที่ถูกเปิดใช้งาน) |
| 1.7 | ปิดใช้งานแบบกลุ่ม (Bulk) | `POST` | `/bulk-deactive/` | บันทึก `BULK_DEACTIVATED` (แสดงจำนวนสมาชิกที่ถูกปิดใช้งาน) |
| 1.8 | ส่งอีเมลเชิญซ้ำ | `POST` | `/{id}/resend-invitation/` | บันทึก `INVITATION_RESENT` |
| 1.9 | เรียกดูประวัติรายคน | `GET` | `/{id}/history-logs/` | แสดงรายการ Log ทั้งหมดที่เกี่ยวข้องกับสมาชิกคนนั้น |

---

## 2. Module: Project (ข้อมูลโครงการ)
**Base Endpoint:** `/api/v1/projects/`

| ลำดับ | การทำงาน (Action) | Method | Endpoint | ผลที่คาดหวัง (Expected Log Event) |
| :--- | :--- | :--- | :--- | :--- |
| 2.1 | อัปโหลดรูปภาพโครงการ | `POST` | `/{id}/image/` | บันทึก `IMAGE_UPLOADED` |
| 2.2 | ลบรูปภาพโครงการ | `DELETE` | `/{id}/image/` | บันทึก `IMAGE_DELETED` |
| 2.3 | เปิดใช้งานโครงการ (Bulk) | `POST` | `/bulk-activate/` | บันทึก `ACTIVATED` (สร้าง Log แยกรายโปรเจกต์) |
| 2.4 | ปิดใช้งานโครงการ (Bulk) | `POST` | `/bulk-deactivate/` | บันทึก `DEACTIVATED` (สร้าง Log แยกรายโปรเจกต์) |
| 2.5 | ซิงค์ข้อมูลจาก SAP รายตัว | `POST` | `/{id}/sap-sync/` | บันทึก `SAP_SYNCED` |
| 2.6 | นำเข้าโปรเจกต์ใหม่ (Import) | `POST` | `/import-sync/` | บันทึก `IMPORTED` เมื่อมีการดึงโปรเจกต์ใหม่จาก SAP สำเร็จ |
| 2.7 | เรียกดูประวัติโครงการ | `GET` | `/{id}/history-logs/` | แสดงรายการ Log ทั้งหมดที่เกี่ยวข้องกับโครงการนั้น |

---

## 3. Module: Project Users (สมาชิกในโครงการ)
**Base Endpoint:** `/api/v1/project-users/`

| ลำดับ | การทำงาน (Action) | Method | Endpoint | ผลที่คาดหวัง (Expected Log Event) |
| :--- | :--- | :--- | :--- | :--- |
| 3.1 | เพิ่มสมาชิกเข้าโปรเจกต์ | `POST` | `/` | บันทึก `MEMBER_ADDED` |
| 3.2 | แก้ไขบทบาท (Role) | `PATCH` | `/{id}/` | บันทึก `MEMBER_UPDATED` (ต้องแสดงรายละเอียดการเปลี่ยนแปลง) |
| 3.3 | ลบสมาชิกออกจากโปรเจกต์ | `DELETE` | `/{id}/` | บันทึก `MEMBER_REMOVED` |
| 3.4 | ลบสมาชิกแบบกลุ่ม (Bulk) | `POST` | `/bulk-delete/` | บันทึก `BULK_MEMBERS_REMOVED` (แสดงจำนวนที่ถูกลบ) |

---

## 4. ระบบสืบค้นและส่งออก (History Search & Export)
**Base Endpoint:** `/api/v1/history/`

| ลำดับ | การทำงาน (Action) | Method | Endpoint | จุดที่ต้องตรวจสอบ (Focus Point) |
| :--- | :--- | :--- | :--- | :--- |
| 4.1 | รายการประวัติทั้งหมด | `GET` | `/` | ตรวจสอบว่า `action_by` และวัตถุที่ถูกกระทำแสดงชื่อที่อ่านออก (Human Readable) |
| 4.2 | การกรองข้อมูล (Filter) | `GET` | `/?action_by=NAME` | ทดสอบ Filter ด้วยชื่อคนทำ หรือรหัส Log (`history_type`) |
| 4.3 | การส่งออกข้อมูล (Export) | `GET` | `/export/` | ตรวจสอบไฟล์ CSV ว่าข้อมูลตรงตามหน้าจอและ Format วันที่ถูกต้อง |

---

## 💡 ข้อแนะนำเพิ่มเติมสำหรับ QA (Focus Points)
1. **Readable Logic:** ในข้อความ Log ต้องไม่แสดงเป็น ID ตัวเลข (เช่น `User #123`) แต่ต้องแสดงเป็นชื่อ-นามสกุล หรือชื่อโปรเจกต์แทน
2. **Data Persistence:** ทดสอบสร้าง Log จากนั้นลอง "ลบ" สมาชิกหรือโปรเจกต์นั้นทิ้ง แล้วกลับมาดูที่หน้า History Log ข้อมูลเดิมต้องยังแสดงเป็นชื่ออยู่ (ไม่ใช่หายไปหรือ Error)
3. **Change Tracking:** ในกรณีการ `Update` ของสมาชิกโปรเจกต์ ให้ตรวจสอบว่าระบบบันทึกค่าเดิม (Old Value) และค่าใหม่ (New Value) ได้ถูกต้องหรือไม่
4. **Actor Mapping:** ตรวจสอบว่า Log บันทึกชื่อผู้กระทำ (Action By) ได้ตรงกับคนที่ Login อยู่ขณะนั้นจริงๆ
