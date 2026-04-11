# Chương 3: Làm việc với Docker Containers

Docker containers là các gói phần mềm nhẹ, độc lập và có thể thực thi được, bao gồm mọi thứ cần thiết để chạy một ứng dụng, bao gồm mã nguồn (code), runtime, các công cụ hệ thống, libraries, và các cài đặt (settings). Trong chương này, chúng ta sẽ khám phá cách làm việc hiệu quả với Docker containers.

## Chạy Container đầu tiên của bạn

Hãy bắt đầu bằng việc chạy một container đơn giản:

```bash
docker run hello-world
```

Lệnh này thực hiện các bước sau:
1. Kiểm tra xem `hello-world` image đã có ở local chưa
2. Nếu không tìm thấy, nó sẽ kéo (pull) image từ Docker Hub
3. Tạo một container từ image đó
4. Chạy container, lệnh này sẽ in ra một thông báo chào mừng
5. Thoát khỏi container

## Các lệnh Docker cơ bản (Basic Docker Commands)

Dưới đây là một số lệnh Docker thiết yếu để làm việc với các containers:

### Liệt kê các Containers

Để xem tất cả các containers đang chạy:
```bash
docker ps
```

Để xem tất cả các containers (bao gồm cả những cái đã dừng):
```bash
docker ps -a
```

### Khởi động và Dừng các Containers

Để dừng một container đang chạy:
```bash
docker stop <container_id_or_name>
```

Để khởi động một container đã dừng:
```bash
docker start <container_id_or_name>
```

Để khởi động lại một container:
```bash
docker restart <container_id_or_name>
```

### Xóa các Containers

Để xóa một container đã dừng:
```bash
docker rm <container_id_or_name>
```

Để ép buộc xóa một container đang chạy:
```bash
docker rm -f <container_id_or_name>
```

## Chạy Containers ở các chế độ khác nhau

### Chế độ Detached Mode

Chạy một container ở chế độ nền (background):
```bash
docker run -d <image_name>
```

### Chế độ Interactive Mode

Chạy một container và tương tác với nó:
```bash
docker run -it <image_name> /bin/bash
```

## Port Mapping (Ánh xạ cổng)

Để ánh xạ một cổng của container với host:
```bash
docker run -p <host_port>:<container_port> <image_name>
```

Ví dụ:
```bash
docker run -d -p 80:80 nginx
```

## Làm việc với Container Logs

Xem logs của container:
```bash
docker logs <container_id_or_name>
```

Theo dõi logs của container trong thời gian thực:
```bash
docker logs -f <container_id_or_name>
```

## Thực thi các lệnh trong Containers đang chạy

Để thực thi một lệnh trong một container đang chạy:
```bash
docker exec -it <container_id_or_name> <command>
```

Ví dụ:
```bash
docker exec -it my_container /bin/bash
```

## Ví dụ thực tế: Chạy một Apache Container

Hãy chạy một Apache web server container:

1. Tải image về:
   ```bash
   docker pull httpd
   ```

2. Chạy container:
   ```bash
   docker run -d --name my-apache -p 8080:80 httpd
   ```

3. Xác minh rằng nó đang chạy:
   ```bash
   docker ps
   ```

4. Truy cập trang mặc định bằng cách mở trình duyệt web và điều hướng đến `http://localhost:8080`

5. Chỉnh sửa trang mặc định:
   ```bash
   docker exec -it my-apache /bin/bash
   echo "<h1>Hello from my Apache container!</h1>" > /usr/local/apache2/htdocs/index.html
   exit
   ```

6. Tải lại trình duyệt của bạn để xem các thay đổi

## Quản lý tài nguyên Container (Container Resource Management)

### Giới hạn Memory

Chạy một container với giới hạn memory:
```bash
docker run -d --memory=512m <image_name>
```

### Giới hạn CPU

Chạy một container với giới hạn CPU:
```bash
docker run -d --cpus=0.5 <image_name>
```

## Mạng của Container (Container Networking)

### Liệt kê các Networks

```bash
docker network ls
```

### Tạo một Network

```bash
docker network create my_network
```

### Kết nối một Container vào một Network

```bash
docker run -d --network my_network --name my_container <image_name>
```

## Lưu trữ dữ liệu với Volumes (Data Persistence with Volumes)

### Tạo một Volume

```bash
docker volume create my_volume
```

### Chạy một Container với một Volume

```bash
docker run -d -v my_volume:/path/in/container <image_name>
```

## Kiểm tra trạng thái Container (Container Health Checks)

Docker cung cấp khả năng kiểm tra sức khỏe (health checking) tích hợp sẵn. Bạn có thể định nghĩa một health check trong Dockerfile của mình:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

## Dọn dẹp (Cleaning Up)

Xóa tất cả các containers đã dừng:
```bash
docker container prune
```

Xóa tất cả các tài nguyên không sử dụng (containers, networks, images):
```bash
docker system prune
```

## Kết luận

Làm việc với Docker containers bao gồm một loạt các hoạt động từ chạy và dừng cơ bản đến các chủ đề nâng cao hơn như quản lý tài nguyên và mạng. Khi bạn trở nên quen thuộc hơn với các thao tác này, bạn sẽ có thể tận dụng toàn bộ tiềm năng của Docker trong quy trình phát triển và triển khai (deployment) của mình.

Hãy nhớ rằng, các containers được thiết kế theo tính chất tạm thời (ephemeral). Luôn lưu trữ dữ liệu quan trọng trong volumes hoặc sử dụng các cơ chế lưu trữ bền vững (persistence) phù hợp cho các ứng dụng của bạn.
