# Chương 12: Xử lý sự cố và Gỡ lỗi Docker (Docker Troubleshooting and Debugging)

Ngay cả khi đã có kế hoạch cẩn thận và áp dụng các quy tắc tốt nhất, các vấn đề vẫn có thể phát sinh khi làm việc với Docker. Chương này đề cập đến các vấn đề phổ biến mà bạn có thể gặp phải và cung cấp các chiến lược để xử lý sự cố (troubleshooting) và gỡ lỗi (debugging) hiệu quả.

## 1. Các vấn đề về Vòng đời Container (Container Lifecycle Issues)

### Container không khởi động được

Nếu một container không khởi động được, hãy sử dụng các lệnh sau:

```bash
# Xem container logs
docker logs <container_id>

# Kiểm tra (Inspect) chi tiết container
docker inspect <container_id>

# Kiểm tra trạng thái container
docker ps -a
```

### Container thoát ngay lập tức

Đối với các containers thoát ngay sau khi khởi động:

```bash
# Chạy container ở chế độ interactive mode
docker run -it --entrypoint /bin/sh <image_name>

# Kiểm tra ENTRYPOINT và CMD trong Dockerfile
docker inspect --format='{{.Config.Entrypoint}}' <image_name>
docker inspect --format='{{.Config.Cmd}}' <image_name>
```

## 2. Các vấn đề về Mạng (Networking Issues)

### Container không thể kết nối mạng

Để xử lý sự cố kết nối mạng:

```bash
# Kiểm tra (Inspect) các cài đặt mạng
docker network inspect <network_name>

# Kiểm tra cài đặt mạng của container
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>

# Sử dụng một container gỡ lỗi mạng
docker run --net container:<container_id> nicolaka/netshoot
```

### Các vấn đề về Ánh xạ cổng (Port Mapping)

Nếu bạn không thể truy cập một cổng đã mở (exposed port) của container:

```bash
# Kiểm tra các ánh xạ cổng (port mappings)
docker port <container_id>

# Xác minh các cài đặt tường lửa (firewall) của host machine
sudo ufw status

# Kiểm tra cổng trực tiếp trên container
docker exec <container_id> nc -zv localhost <port>
```

## 3. Các vấn đề về Lưu trữ và Volume (Storage and Volume Issues)

### Các vấn đề về Lưu trữ bền vững dữ liệu

Đối với các vấn đề về dữ liệu không được lưu trữ bền vững:

```bash
# Liệt kê các volumes
docker volume ls

# Kiểm tra (Inspect) một volume
docker volume inspect <volume_name>

# Kiểm tra các volume mounts trong một container
docker inspect --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{"\n"}}{{end}}' <container_id>
```

### Các vấn đề về Dung lượng đĩa

Nếu bạn sắp hết dung lượng đĩa:

```bash
# Kiểm tra việc sử dụng đĩa của Docker
docker system df

# Xóa các dữ liệu không sử dụng
docker system prune -a

# Xác định các images lớn
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -h
```

## 4. Các ràng buộc tài nguyên (Resource Constraints)

### Container sử dụng quá nhiều CPU hoặc Memory

Để xác định và giải quyết các vấn đề sử dụng tài nguyên:

```bash
# Theo dõi việc sử dụng tài nguyên
docker stats

# Thiết lập giới hạn tài nguyên
docker run --memory=512m --cpus=0.5 <image_name>

# Cập nhật giới hạn cho một container đang chạy
docker update --cpus=0.75 <container_id>
```

## 5. Các vấn đề liên quan đến Image

### Lỗi Pull Image

Nếu bạn không thể pull một image:

```bash
# Kiểm tra trạng thái Docker Hub
curl -Is https://registry.hub.docker.com/v2/ | head -n 1

# Xác minh đăng nhập Docker của bạn
docker login

# Thử pull với đầu ra chi tiết (verbose output)
docker pull --verbose <image_name>
```

### Lỗi Build Image

Đối với các vấn đề trong quá trình build image:

```bash
# Build với đầu ra chi tiết
docker build --progress=plain -t <image_name> .

# Kiểm tra các vấn đề trong Dockerfile
docker build --no-cache -t <image_name> .
```

## 6. Các vấn đề về Docker Daemon

### Docker Daemon không khởi động được

Nếu Docker daemon không khởi động được:

```bash
# Kiểm tra trạng thái Docker daemon
sudo systemctl status docker

# Xem Docker daemon logs
sudo journalctl -u docker.service

# Khởi động lại Docker daemon
sudo systemctl restart docker
```

## 7. Các kỹ thuật Gỡ lỗi (Debugging Techniques)

### Gỡ lỗi tương tác (Interactive Debugging)

Để gỡ lỗi một container đang chạy một cách tương tác:

```bash
# Bắt đầu một shell tương tác trong một container đang chạy
docker exec -it <container_id> /bin/bash

# Chạy một container mới với một shell để gỡ lỗi
docker run -it --entrypoint /bin/bash <image_name>
```

### Sử dụng Docker Events

Theo dõi Docker events để xử lý sự cố:

```bash
docker events
```

### Ghi nhật ký (Logging)

Cấu hình và xem logs của container:

```bash
# Xem container logs
docker logs <container_id>

# Theo dõi đầu ra của log
docker logs -f <container_id>

# Điều chỉnh trình điều khiển ghi nhật ký (logging driver)
docker run --log-driver json-file --log-opt max-size=10m <image_name>
```

## 8. Gỡ lỗi Hiệu suất (Performance Debugging)

### Xác định các nút thắt cổ chai hiệu suất (Performance Bottlenecks)

Sử dụng các lệnh sau để xác định các vấn đề về hiệu suất:

```bash
# Theo dõi việc sử dụng tài nguyên container
docker stats

# Phân tích các tiến trình của container
docker top <container_id>

# Sử dụng cAdvisor để có các số liệu (metrics) chi tiết hơn
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:latest
```

## 9. Xử lý sự cố với Docker Compose

Đối với các vấn đề với Docker Compose:

```bash
# Xem logs cho tất cả các services
docker-compose logs

# Build lại và tạo lại các containers
docker-compose up -d --build

# Kiểm tra cấu hình (configuration)
docker-compose config
```

## Kết luận

Kỹ năng xử lý sự cố (troubleshooting) và gỡ lỗi (debugging) hiệu quả là những kỹ năng thiết yếu khi làm việc với Docker. Bằng cách hiểu rõ các kỹ thuật và công cụ này, bạn có thể nhanh chóng xác định và giải quyết các vấn đề trong môi trường Docker của mình. Đừng quên luôn kiểm tra tài liệu chính thức của Docker và các diễn đàn cộng đồng để có những thông tin cập nhật nhất và các giải pháp cho những vấn đề phổ biến.
