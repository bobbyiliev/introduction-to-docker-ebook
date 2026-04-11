# Chương 11: Tối ưu hóa hiệu suất Docker (Docker Performance Optimization)

Tối ưu hóa hiệu suất (performance) Docker là yếu tố then chốt để sử dụng tài nguyên hiệu quả và cải thiện khả năng phản hồi của ứng dụng. Chương này bao gồm các kỹ thuật và quy tắc tốt nhất (best practices) khác nhau để nâng cao hiệu suất của các Docker containers và toàn bộ môi trường Docker của bạn.

## 1. Tối ưu hóa Docker Images

### Sử dụng Multi-Stage Builds

Multi-stage builds có thể giảm đáng kể kích thước của Docker image cuối cùng:

```dockerfile
# Build stage
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

# Final stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
CMD ["./main"]
```

### Tối thiểu hóa số lượng Layers (Layer Count)

Kết hợp các lệnh để giảm số lượng các layers:

```dockerfile
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    package3 \
 && rm -rf /var/lib/apt/lists/*
```

### Sử dụng .dockerignore

Tạo một file `.dockerignore` để loại bỏ các tệp không cần thiết khỏi build context:

```
.git
*.md
*.log
```

## 2. Quản lý tài nguyên Container (Container Resource Management)

### Thiết lập giới hạn Memory và CPU

```yaml
version: '3'
services:
  app:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### Sử dụng --cpuset-cpus để ghim CPU (CPU Pinning)

```bash
docker run --cpuset-cpus="0,1" myapp
```

## 3. Tối ưu hóa Mạng (Networking Optimization)

### Sử dụng chế độ Host Networking Mode

Đối với các kịch bản yêu cầu hiệu suất cao, hãy cân nhắc sử dụng host networking:

```bash
docker run --network host myapp
```

### Tối ưu hóa phân giải DNS (DNS Resolution)

Nếu bạn gặp tình trạng phân giải DNS chậm, bạn có thể sử dụng tùy chọn `--dns`:

```bash
docker run --dns 8.8.8.8 myapp
```

## 4. Tối ưu hóa Lưu trữ (Storage Optimization)

### Sử dụng Volumes thay vì Bind Mounts

Volumes thường mang lại hiệu suất tốt hơn so với bind mounts:

```yaml
version: '3'
services:
  db:
    image: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Cân nhắc sử dụng tmpfs Mounts

Đối với dữ liệu tạm thời (ephemeral data), tmpfs mounts có thể cải thiện hiệu suất I/O:

```bash
docker run --tmpfs /tmp myapp
```

## 5. Ghi nhật ký và Giám sát (Logging and Monitoring)

### Sử dụng JSON-file Logging Driver với các giới hạn

```yaml
version: '3'
services:
  app:
    image: myapp
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### Triển khai hệ thống Giám sát phù hợp

Sử dụng các công cụ như Prometheus và Grafana để giám sát toàn diện:

```yaml
version: '3'
services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
```

## 6. Tối ưu hóa Docker Daemon

### Điều chỉnh Storage Driver

Cân nhắc sử dụng overlay2 để có hiệu suất tốt hơn:

```json
{
  "storage-driver": "overlay2"
}
```

### Kích hoạt Live Restore

Tính năng này cho phép các containers tiếp tục chạy ngay cả khi Docker daemon không khả dụng:

```json
{
  "live-restore": true
}
```

## 7. Tối ưu hóa mức Ứng dụng (Application-Level Optimization)

### Sử dụng các Images Alpine-Based

Các images Alpine-based thường nhỏ hơn và tốc độ pull nhanh hơn:

```dockerfile
FROM alpine:3.14
RUN apk add --no-cache python3
```

### Tối ưu hóa mã nguồn Ứng dụng của bạn

Đảm bảo ứng dụng của bạn được tối ưu hóa cho môi trường container hóa (containerized environments):
- Triển khai các cơ chế tải tạm (caching) phù hợp
- Tối ưu hóa các truy vấn database
- Sử dụng xử lý bất đồng bộ (asynchronous processing) khi phù hợp

## 8. Đo lường và Phân tích hiệu năng (Benchmarking and Profiling)

### Sử dụng lệnh Stats tích hợp sẵn của Docker

```bash
docker stats
```

### Đo hiệu năng (Benchmark) với các công cụ như Apache Bench

```bash
ab -n 1000 -c 100 http://localhost/
```

## 9. Tối ưu hóa mức Điều phối (Orchestration-Level Optimization)

Khi sử dụng các công cụ điều phối (orchestration) như Kubernetes:

### Sử dụng Horizontal Pod Autoscaler (HPA)

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

### Triển khai Liveness and Readiness Probes phù hợp

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
```

## Kết luận

Tối ưu hóa hiệu suất (performance) Docker là một quy trình liên tục bao gồm nhiều khía cạnh khác nhau trong thiết lập Docker của bạn, từ việc build image cho đến cấu hình runtime và tối ưu hóa mức ứng dụng. Bằng cách thực hiện các quy tắc tốt nhất này và liên tục giám sát môi trường Docker, bạn có thể cải thiện đáng kể hiệu suất và hiệu quả của các ứng dụng container hóa của mình.
