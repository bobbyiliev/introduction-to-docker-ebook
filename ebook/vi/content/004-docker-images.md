# Chương 4: Docker Images là gì?

Một Docker image là một gói phần mềm nhẹ, độc lập và có thể thực thi được, bao gồm mọi thứ cần thiết để chạy một ứng dụng, bao gồm mã nguồn (code), runtime, các công cụ hệ thống, libraries, và các cài đặt (settings). Images là những thành phần cơ bản (building blocks) của Docker containers.

## Các khái niệm chính (Key Concepts)

1. **Layers (Các lớp)**: Các Images được cấu thành từ nhiều layers, mỗi layer đại diện cho một tập hợp các thay đổi đối với filesystem.
2. **Base Image (Image cơ sở)**: Nền tảng của một image, thường là một hệ điều hành tối giản.
3. **Parent Image (Image cha)**: Một image mà image của bạn được xây dựng dựa trên đó.
4. **Image Tags (Thẻ Image)**: Các nhãn được sử dụng để đánh phiên bản và xác định các images.
5. **Image ID**: Một định danh duy nhất cho mỗi image.

## Làm việc với Docker Images

### Liệt kê các Images

Để xem tất cả các images trên hệ thống local của bạn:

```bash
docker images
```

Hoặc sử dụng lệnh chi tiết (verbose) hơn:

```bash
docker image ls
```

### Pulling Images từ Docker Hub

Để tải một image từ Docker Hub:

```bash
docker pull <image_name>:<tag>
```

Ví dụ:

```bash
docker pull ubuntu:20.04
```

Nếu không chỉ định tag, theo mặc định Docker sẽ pull tag `latest`.

### Chạy Containers từ Images

Để chạy một container từ một image:

```bash
docker run <image_name>:<tag>
```

Ví dụ:

```bash
docker run -it ubuntu:20.04 /bin/bash
```

### Thông tin về Image

Để lấy thông tin chi tiết về một image:

```bash
docker inspect <image_name>:<tag>
```

### Xóa các Images

Để xóa một image:

```bash
docker rmi <image_name>:<tag>
```

hoặc

```bash
docker image rm <image_name>:<tag>
```

Để xóa tất cả các images không sử dụng:

```bash
docker image prune
```

## Xây dựng các Custom Images

### Sử dụng một Dockerfile

1. Tạo một file tên là `Dockerfile` (không có phần mở rộng).
2. Định nghĩa các chỉ dẫn để build image của bạn.

Ví dụ Dockerfile:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY ./my-nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

3. Build image:

```bash
docker build -t my-nginx:v1 .
```

### Build từ một Container đang chạy

1. Thực hiện các thay đổi đối với một container đang chạy.
2. Tạo một image mới từ container đó:

```bash
docker commit <container_id> my-new-image:tag
```

## Gắn thẻ Image (Image Tagging)

Để gắn thẻ cho một image hiện có:

```bash
docker tag <source_image>:<tag> <target_image>:<tag>
```

Ví dụ:

```bash
docker tag my-nginx:v1 my-dockerhub-username/my-nginx:v1
```

## Đưa Images lên Docker Hub (Pushing Images)

1. Đăng nhập vào Docker Hub:

```bash
docker login
```

2. Push image:

```bash
docker push my-dockerhub-username/my-nginx:v1
```

## Image Layers và Caching

Hiểu về layers là cực kỳ quan trọng để tối ưu hóa việc build image:

1. Mỗi chỉ dẫn trong một Dockerfile tạo ra một layer mới.
2. Các layers được lưu trữ tạm thời (cached) và được tái sử dụng trong các lần build sau.
3. Việc sắp xếp các chỉ dẫn từ ít thay đổi nhất đến thường xuyên thay đổi nhất có thể tăng tốc độ build.

Ví dụ về việc tận dụng caching:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY ./static-files /var/www/html
COPY ./config-files /etc/nginx
```

## Multi-stage Builds

Multi-stage builds cho phép bạn sử dụng nhiều câu lệnh FROM trong Dockerfile của mình. Điều này hữu ích để tạo ra các production images nhỏ gọn hơn.

Ví dụ:

```dockerfile
# Build stage
FROM golang:1.16 AS build
WORKDIR /app
COPY . .
RUN go build -o myapp

# Production stage
FROM alpine:3.14
COPY --from=build /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

## Quét Image và Bảo mật (Image Scanning and Security)

Docker cung cấp khả năng quét image (image scanning) tích hợp sẵn:

```bash
docker scan <image_name>:<tag>
```

Việc này giúp xác định các lỗ hổng (vulnerabilities) trong images của bạn.

## Các quy tắc tốt nhất khi làm việc với Images (Best Practices)

1. Sử dụng các tags cụ thể thay vì `latest` để đảm bảo tính tái lập (reproducibility).
2. Giữ cho images nhỏ gọn bằng cách sử dụng các base images tối giản và multi-stage builds.
3. Sử dụng `.dockerignore` để loại bỏ các tệp không cần thiết khỏi build context.
4. Tận dụng build cache bằng cách sắp xếp các chỉ dẫn Dockerfile một cách hiệu quả.
5. Cập nhật các base images thường xuyên để nhận các bản vá bảo mật (security patches).
6. Quét images để tìm các lỗ hổng trước khi triển khai (deployment).

## Quản lý và Dọn dẹp Image

Để quản lý dung lượng đĩa, hãy thường xuyên dọn dẹp các images không sử dụng:

```bash
docker system prune -a
```

Lệnh này sẽ xóa tất cả các images không sử dụng, không chỉ riêng các images bị treo (dangling).

## Kết luận

Docker images là một khái niệm nền tảng trong containerization. Chúng cung cấp một cách nhất quán và linh hoạt để đóng gói ứng dụng và các dependencies của chúng. Bằng cách làm chủ việc tạo, tối ưu hóa và quản lý image, bạn có thể cải thiện đáng kể quy trình làm việc với Docker và việc triển khai ứng dụng.
