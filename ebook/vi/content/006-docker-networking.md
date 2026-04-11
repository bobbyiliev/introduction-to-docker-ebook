# Chương 6: Docker Networking

Docker networking cho phép các containers giao tiếp với nhau và với thế giới bên ngoài. Đây là một khía cạnh quan trọng của Docker giúp tạo ra các ứng dụng đa container (multi-container applications) phức tạp và các kiến trúc microservices (microservices architectures).

## Trình điều khiển mạng Docker (Docker Network Drivers)

Docker sử dụng một kiến trúc cắm (pluggable architecture) cho networking, cung cấp nhiều trình điều khiển mạng (network drivers) tích hợp sẵn:

1. **Bridge**: Trình điều khiển mạng mặc định. Nó phù hợp cho các containers đứng độc lập cần giao tiếp với nhau.
2. **Host**: Loại bỏ sự cô lập mạng giữa container và Docker host.
3. **Overlay**: Cho phép giao tiếp giữa các containers trên nhiều máy chạy Docker daemon khác nhau.
4. **MacVLAN**: Gán một địa chỉ MAC cho một container, làm cho nó xuất hiện như một thiết bị vật lý trên mạng.
5. **None**: Vô hiệu hóa tất cả các kết nối mạng cho một container.
6. **Network plugins**: Cho phép bạn sử dụng các trình điều khiển mạng (network drivers) của bên thứ ba.

## Làm việc với Docker Networks

### Liệt kê các Mạng

Để liệt kê tất cả các mạng:

```bash
docker network ls
```

Lệnh này hiển thị network ID, tên, trình điều khiển (driver) và phạm vi (scope) của mỗi mạng.

### Kiểm tra Mạng (Inspecting Networks)

Để lấy thông tin chi tiết về một mạng:

```bash
docker network inspect <network_name>
```

Lệnh này cung cấp thông tin như subnet, gateway, các containers đang kết nối (connected containers) và các tùy chọn cấu hình của mạng.

### Tạo một Mạng

Để tạo một mạng mới:

```bash
docker network create --driver <driver> <network_name>
```

Ví dụ:

```bash
docker network create --driver bridge my_custom_network
```

Bạn có thể chỉ định các tùy chọn bổ sung như subnet, gateway, dải IP (IP range), v.v.:

```bash
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 my_custom_network
```

### Kết nối Containers vào Mạng

Khi chạy một container, bạn có thể chỉ định mạng mà nó sẽ kết nối vào:

```bash
docker run --network <network_name> <image>
```

Ví dụ:

```bash
docker run --network my_custom_network --name container1 -d nginx
```

Bạn cũng có thể kết nối một container đang chạy vào một mạng:

```bash
docker network connect <network_name> <container_name>
```

### Ngắt kết nối Containers khỏi Mạng

Để ngắt kết nối một container khỏi một mạng:

```bash
docker network disconnect <network_name> <container_name>
```

### Xóa Mạng

Để xóa một mạng:

```bash
docker network rm <network_name>
```

## Đi sâu vào các Trình điều khiển Mạng (Network Drivers)

### Mạng Bridge (Bridge Networks)

Mạng Bridge là loại mạng được sử dụng phổ biến nhất trong Docker. Chúng phù hợp cho các containers chạy trên cùng một máy Docker daemon host.

Các điểm chính về mạng Bridge:

- Mỗi container kết nối vào mạng Bridge được cấp một địa chỉ IP duy nhất.
- Các containers trên cùng một mạng Bridge có thể giao tiếp với nhau thông qua địa chỉ IP.
- Mạng Bridge mặc định có một số hạn chế, vì vậy thường tốt hơn nếu bạn tạo các mạng Bridge tùy chỉnh (custom bridge networks).

Ví dụ về việc tạo và sử dụng một mạng Bridge tùy chỉnh:

```bash
docker network create my_bridge
docker run --network my_bridge --name container1 -d nginx
docker run --network my_bridge --name container2 -d nginx
```

Bây giờ `container1` và `container2` có thể giao tiếp với nhau bằng cách sử dụng tên container của chúng làm hostnames.

### Mạng Host (Host Networks)

Host networking thêm một container vào ngăn xếp mạng (network stack) của host. Điều này mang lại hiệu suất mạng tốt nhất nhưng làm mất đi sự cô lập mạng.

Ví dụ:

```bash
docker run --network host -d nginx
```

Trong trường hợp này, nếu container mở (expose) cổng 80, nó sẽ có thể truy cập trực tiếp trên cổng 80 của host machine.

### Mạng Overlay (Overlay Networks)

Mạng Overlay được sử dụng trong chế độ Docker Swarm mode để cho phép giao tiếp giữa các containers trên nhiều máy Docker daemon host khác nhau.

Để tạo một mạng Overlay:

```bash
docker network create --driver overlay my_overlay
```

Sau đó, khi tạo một service trong chế độ swarm mode, bạn có thể gắn nó vào mạng này:

```bash
docker service create --network my_overlay --name my_service nginx
```

### Mạng MacVLAN (MacVLAN Networks)

Mạng MacVLAN cho phép bạn gán một địa chỉ MAC cho một container, làm cho nó xuất hiện như một thiết bị vật lý trên mạng của bạn.

Ví dụ:

```bash
docker network create -d macvlan \
  --subnet=192.168.0.0/24 \
  --gateway=192.168.0.1 \
  -o parent=eth0 my_macvlan_net
```

Sau đó chạy một container trên mạng này:

```bash
docker run --network my_macvlan_net -d nginx
```

## Xử lý sự cố Mạng (Network Troubleshooting)

1. **Giao tiếp giữa Container với Container**: 
   Sử dụng lệnh `docker exec` để đi vào trong container và sử dụng các công cụ như `ping`, `curl`, hoặc `wget` để kiểm tra kết nối.

2. **Kiểm tra Mạng (Network Inspection)**: 
   Sử dụng `docker network inspect` để xem thông tin chi tiết về một mạng.

3. **Ánh xạ cổng (Port Mapping)**: 
   Sử dụng `docker port <container>` để xem các ánh xạ cổng của một container.

4. **Sự cố DNS**: 
   Kiểm tra tệp `/etc/resolv.conf` bên trong container để xác minh các cài đặt DNS.

5. **Network Namespace**: 
   Để xử lý sự cố nâng cao, bạn có thể đi vào network namespace của một container:
   ```bash
   pid=$(docker inspect -f '{{.State.Pid}}' <container_name>)
   nsenter -t $pid -n ip addr
   ```

## Các quy tắc tốt nhất (Best Practices)

1. Sử dụng các mạng Bridge tùy chỉnh (custom bridge networks) thay vì mạng Bridge mặc định để cô lập tốt hơn và có tính năng phân giải tên miền (built-in DNS resolution) tích hợp sẵn.
2. Sử dụng mạng Overlay để giao tiếp đa máy (multi-host communication) trong chế độ swarm mode.
3. Sử dụng host networking một cách tiết kiệm và chỉ khi yêu cầu hiệu suất cao.
4. Cẩn trọng khi mở (exposing) các cổng, chỉ mở những gì thực sự cần thiết.
5. Sử dụng Docker Compose để quản lý các ứng dụng đa container và mạng của chúng.

## Các chủ đề nâng cao

### Mã hóa mạng (Network Encryption)

Đối với mạng Overlay, bạn có thể bật mã hóa để bảo mật lưu lượng giữa container với container:

```bash
docker network create --opt encrypted --driver overlay my_secure_network
```

### Trình cắm mạng (Network Plugins)

Docker hỗ trợ các trình cắm mạng (network plugins) của bên thứ ba. Các tùy chọn phổ biến bao gồm Weave Net, Calico và Flannel. Những trình cắm này có thể cung cấp các tính năng bổ sung như định tuyến nâng cao (advanced routing), chính sách mạng (network policies) và mã hóa.

### Khám phá dịch vụ (Service Discovery)

Docker cung cấp tính năng khám phá dịch vụ (service discovery) tích hợp sẵn cho các containers trên cùng một mạng. Các containers có thể liên lạc với nhau bằng cách sử dụng tên container làm hostnames. Trong chế độ swarm mode, cũng có sẵn tính năng cân bằng tải (built-in load balancing) cho các dịch vụ.

## Kết luận

Networking là một thành phần quan trọng của Docker giúp thực hiện các ứng dụng phân tán, phức tạp. Bằng cách hiểu và sử dụng hiệu quả các khả năng networking của Docker, bạn có thể tạo ra các ứng dụng container hóa bảo mật, hiệu quả và có khả năng mở rộng. Luôn xem xét trường hợp sử dụng cụ thể của bạn khi chọn các trình điều khiển mạng (network drivers) và cấu hình.
