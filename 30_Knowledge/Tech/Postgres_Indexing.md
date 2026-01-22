---
type: knowledge
topic: Database
tags: [database, postgres, sql, performance]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# PostgreSQL Indexing Strategies

## 📌 Concept
Index คือสารบัญของหนังสือ ช่วยให้ Database หาข้อมูลเจอโดยไม่ต้องเปิดอ่านทุกหน้า (Full Table Scan) การเลือก Index ผิดประเภทอาจทำให้ Insert ช้าลงและเปลืองพื้นที่

## 💻 How it works / Code

### 1. B-Tree (Default & Most Common)
ใช้กับข้อมูลทั่วไป และการเปรียบเทียบ `<`, `<=`, `=`, `>=`, `>`
```sql
CREATE INDEX idx_users_email ON users (email);
-- เหมาะกับ: SELECT * FROM users WHERE email = 'test@example.com';
```

### 2. GIN (Generalized Inverted Index)
เทพเจ้าแห่ง **JSONB** และ **Full Text Search**
```sql
-- สมมติ users มี column 'data' เป็น JSONB
CREATE INDEX idx_users_data ON users USING GIN (data);
-- เหมาะกับ: SELECT * FROM users WHERE data @> '{"role": "admin"}';
```

### 3. Partial Index (Index แค่บางส่วน)
ประหยัดพื้นที่มหาศาล ถ้าเรารู้ว่าจะ query แค่ subset ของข้อมูล
```sql
-- Index เฉพาะ user ที่ active เท่านั้น (inactive ไม่ต้อง index ให้เปลือง)
CREATE INDEX idx_active_users ON users (created_at) WHERE is_active = true;
```

### 4. Composite Index (Index หลายคอลัมน์)
ลำดับสำคัญมาก! (Left-to-Right rule)
```sql
CREATE INDEX idx_name_age ON users (lastname, age);
-- ✅ ใช้ได้: WHERE lastname = 'Doe' AND age = 30
-- ✅ ใช้ได้: WHERE lastname = 'Doe'
-- ❌ ใช้ไม่ได้: WHERE age = 30 (เพราะข้าม lastname ไป)
```

## 🔗 References
- [PostgreSQL Indexes Documentation](https://www.postgresql.org/docs/current/indexes.html)
- [Use The Index, Luke!](https://use-the-index-luke.com/)
