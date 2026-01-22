---
type: knowledge
topic: DevOps
tags: [docker, devops, container, linux]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# Docker Best Practices

## 📌 Concept
การเขียน Dockerfile ที่ดีควรเน้น 3 เรื่อง: **Size** (เล็ก), **Security** (ปลอดภัย), และ **Build Speed** (Cache ได้ดี) เทคนิคที่สำคัญที่สุดคือ **Multi-stage Build**

## 💻 How it works / Code

### The "Bad" Way ❌
```dockerfile
FROM ubuntu
COPY . .
RUN apt-get update && apt-get install -y golang
RUN go build -o app main.go
CMD ["./app"]
# ผลลัพธ์: Image ใหญ่มาก เพราะมีทั้ง Source code, Compiler, และ OS tools
```

### The "Arch Way" (Multi-stage) ✅
```dockerfile
# Stage 1: Build
FROM golang:1.21-alpine AS builder
WORKDIR /app
# Copy go.mod ก่อน เพื่อให้ Docker Cache layer นี้ถ้า dependencies ไม่เปลี่ยน
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o myapp .

# Stage 2: Runtime (Distroless or Alpine)
FROM gcr.io/distroless/static-debian12
COPY --from=builder /app/myapp /
CMD ["/myapp"]
# ผลลัพธ์: Image เหลือแค่ ~10MB (มีแค่ Binary เพียวๆ)
```

### 🔑 Key Checklist
1.  **Use specific tags**: อย่าใช้ `:latest` ให้ระบุ version เช่น `node:18-alpine`
2.  **Order matters**: เอาคำสั่งที่เปลี่ยนแปลงบ่อย (COPY code) ไว้ล่างสุด เพื่อใช้ประโยชน์จาก Layer Cache
3.  **Run as non-root**: เพื่อความปลอดภัย (Distroless ทำมาให้แล้ว หรือใช้ `USER 1001`)

## 🔗 References
- [Docker Official Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Distroless Images](https://github.com/GoogleContainerTools/distroless)
