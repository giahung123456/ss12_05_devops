# Bài 5 – Troubleshooting CI/CD Pipeline

## 1. Các lỗi và cách khắc phục

### Lỗi 1: Gradle Wrapper Permission Denied

**Biểu hiện:** GitHub Actions báo `./gradlew: Permission denied`, exit code 126.

**Nguyên nhân:** File `gradlew` chưa có quyền thực thi trên môi trường Linux.

**Cách khắc phục:** Thêm bước cấp quyền trước khi chạy Gradle:

```yaml
- name: Grant execute permission to Gradle Wrapper
  run: chmod +x gradlew
```

**Kết quả:** Bước cấp quyền và bước `Run tests` chạy thành công.

### Lỗi 2: GHCR Authentication Failed

**Biểu hiện:** Bước đăng nhập GHCR báo `Password required`.

**Nguyên nhân:** Tên secret được tham chiếu không đúng hoặc không có giá trị.

**Cách khắc phục:** Sử dụng `GITHUB_TOKEN` có sẵn và cấp quyền `packages: write`:

```yaml
permissions:
  contents: read
  packages: write
```

```yaml
password: ${{ secrets.GITHUB_TOKEN }}
```

**Kết quả:** Bước `Log in to GHCR` chạy thành công.

### Lỗi 3: Docker Build Context Sai

**Nguyên nhân:** Workflow trỏ tới `./docker/Dockerfile`, trong khi Dockerfile nằm ở thư mục gốc dự án.

**Cách khắc phục:**

```yaml
context: .
file: ./Dockerfile
```

### Lỗi bổ sung: Docker Image Tag không hợp lệ

**Biểu hiện:** Docker báo `repository name must be lowercase`.

**Nguyên nhân:** Tag được tạo từ tên repository có chứa chữ hoa.

**Cách khắc phục:** Sử dụng tên image viết thường, ví dụ:

```yaml
tags: ghcr.io/dunghueng/homework_session_12_ex5:latest
```

## 2. Build ứng dụng trước khi đóng gói Docker

Thêm bước tạo JAR sau bước kiểm thử:

```yaml
- name: Build Spring Boot JAR
  run: ./gradlew bootJar
```