# Chương 13: Các khái niệm và Tính năng Docker nâng cao

Khi bạn trở nên thành thạo hơn với Docker, bạn sẽ bắt gặp các khái niệm và tính năng nâng cao hơn. Chương này khám phá một số chủ đề như vậy để giúp bạn đưa kỹ năng Docker của mình lên một tầm cao mới, mặc dù điều này nằm ngoài phạm vi của cuốn ebook giới thiệu này.

## 1. Multi-stage Builds

Multi-stage builds cho phép bạn tạo ra các Dockerfiles hiệu quả hơn bằng cách sử dụng nhiều câu lệnh FROM trong Dockerfile của mình.

```dockerfile
# Build stage
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Final stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
CMD ["./main"]
```

Cách tiếp cận này giúp giảm kích thước image cuối cùng bằng cách chỉ bao gồm các thành phần (artifacts) cần thiết từ build stage.

## 2. Docker BuildKit

BuildKit là một công cụ build (build engine) thế hệ mới cho Docker. Hãy kích hoạt nó bằng cách thiết lập một biến môi trường:

```bash
export DOCKER_BUILDKIT=1
```

BuildKit cung cấp tốc độ build nhanh hơn, quản lý cache tốt hơn và các tính năng nâng cao như:

- Phân giải phụ thuộc đồng thời (Concurrent dependency resolution)
- Cache các chỉ dẫn hiệu quả (Efficient instruction caching)
- Tự động dọn rác (Automatic garbage collection)

## 3. Các mạng Bridge tùy chỉnh (Custom Bridge Networks)

Tạo các môi trường mạng bị cô lập (isolated network environments) cho các containers của bạn:

```bash
docker network create --driver bridge isolated_network
docker run --network=isolated_network --name container1 -d nginx
docker run --network=isolated_network --name container2 -d nginx
```

Các containers trên mạng này có thể giao tiếp với nhau bằng cách sử dụng tên của chúng làm hostnames.

## 4. Docker Contexts

Quản lý nhiều môi trường Docker với contexts:

```bash
# Tạo một context mới
docker context create my-remote --docker "host=ssh://user@remote-host"

# Liệt kê các contexts
docker context ls

# Chuyển đổi context
docker context use my-remote
```

## 5. Docker Content Trust (DCT)

DCT cung cấp một cách để xác minh tính toàn vẹn và thực thể xuất bản của các images:

```bash
# Kích hoạt DCT
export DOCKER_CONTENT_TRUST=1

# Push một image đã được ký (signed image)
docker push myrepo/myimage:latest
```

## 6. Docker Secrets

Quản lý các dữ liệu nhạy cảm với Docker secrets:

```bash
# Tạo một secret
echo "mypassword" | docker secret create my_secret -

# Sử dụng secret trong một service
docker service create --name myservice --secret my_secret myimage
```

## 7. Docker Health Checks

Triển khai các kiểm tra sức khỏe tùy chỉnh trong Dockerfile của bạn:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

## 8. Docker Plugins

Mở rộng chức năng của Docker với các plugins:

```bash
# Cài đặt một plugin
docker plugin install vieux/sshfs

# Sử dụng plugin
docker volume create -d vieux/sshfs -o sshcmd=user@host:/path sshvolume
```

## 9. Các tính năng Docker thực nghiệm (Experimental Features)

Kích hoạt các tính năng thực nghiệm (experimental features) trong cấu hình Docker daemon (`/etc/docker/daemon.json`):

```json
{
  "experimental": true
}
```

Điều này mở khóa các tính năng như:

- Checkpoint and restore
- Rootless mode

## 10. Bảo vệ chống thoát Container (Container Escape Protection)

Sử dụng các tùy chọn bảo mật để ngăn chặn việc thoát khỏi container:

```bash
docker run --security-opt="no-new-privileges:true" --cap-drop=ALL myimage
```

## 11. Các chỉ dẫn Dockerfile tùy chỉnh

Tạo các chỉ dẫn Dockerfile tùy chỉnh bằng cách sử dụng ONBUILD:

```dockerfile
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
```

## 12. Docker Manifest

Tạo và push các images đa kiến trúc (multi-architecture images):

```bash
docker manifest create myrepo/myimage myrepo/myimage:amd64 myrepo/myimage:arm64
docker manifest push myrepo/myimage
```

## 13. Docker Buildx

Buildx là một CLI plugin mở rộng câu lệnh `docker build` với sự hỗ trợ đầy đủ các tính năng do BuildKit cung cấp:

```bash
# Tạo một builder instance mới
docker buildx create --name mybuilder

# Build và push các images đa nền tảng (multi-platform images)
docker buildx build --platform linux/amd64,linux/arm64 -t myrepo/myimage:latest --push .
```

## 14. Docker Compose Profiles

Sử dụng các profiles trong Docker Compose để khởi chạy các services một cách có chọn lọc:

```yaml
services:
  frontend:
    image: frontend
    profiles: ["frontend"]
  backend:
    image: backend
    profiles: ["backend"]
```

 Khởi chạy các profiles cụ thể:

```bash
docker-compose --profile frontend up -d
```

## Kết luận

Các khái niệm và tính năng Docker nâng cao này cung cấp các công cụ mạnh mẽ để tối ưu hóa quy trình làm việc với Docker, cải thiện bảo mật và mở rộng khả năng của Docker. Khi bạn áp dụng những kỹ thuật này vào các dự án của mình, bạn sẽ có thể tạo ra các môi trường Docker hiệu quả, an toàn và linh hoạt hơn.
