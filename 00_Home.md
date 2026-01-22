# 🖥️ Work OS Dashboard

## 🔥 Active Projects
```dataview
TABLE status, stack
FROM "20_Projects"
WHERE status = "active"
```

## 📅 Recent Weekly Reviews
```dataview
TABLE impact_score as "Impact", rows.file.link as "Week"
FROM "10_Journal/Weekly"
WHERE type = "weekly-review"
SORT created DESC
LIMIT 5
```

## 🧠 Latest Knowledge
```dataview
LIST
FROM "30_Knowledge"
WHERE publish = true
SORT file.mtime DESC
LIMIT 5
```
