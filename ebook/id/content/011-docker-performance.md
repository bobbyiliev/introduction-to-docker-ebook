# Bab 11: Optimasi Performa Docker

Mengoptimalkan performa Docker sangat penting untuk pemanfaatan sumber daya yang efisien dan respons aplikasi yang lebih baik. Bab ini membahas berbagai teknik dan praktik terbaik untuk meningkatkan performa container Docker dan lingkungan Docker secara keseluruhan.

## 1. Optimasi Docker Image

### Gunakan Multi-Stage Build

Multi-stage build dapat secara signifikan mengurangi ukuran image Docker akhir Anda:

```dockerfile
# Tahap build
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .

# Tahap akhir
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
CMD ["./main"]
```

### Minimalkan Jumlah Layer

Gabungkan perintah untuk mengurangi jumlah layer:

```dockerfile
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    package3 \
 && rm -rf /var/lib/apt/lists/*
```

### Gunakan .dockerignore

Buat file `.dockerignore` untuk mengecualikan file yang tidak diperlukan dari konteks build:

```
.git
*.md
*.log
```

## 2. Manajemen Resource Container

### Atur Batas Memori dan CPU

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

### Gunakan --cpuset-cpus untuk CPU Pinning

```bash
docker run --cpuset-cpus="0,1" myapp
```

## 3. Optimasi Jaringan

### Gunakan Mode Jaringan Host

Untuk skenario performa tinggi, pertimbangkan menggunakan jaringan host:

```bash
docker run --network host myapp
```

### Optimasi Resolusi DNS

Jika mengalami resolusi DNS yang lambat, gunakan opsi `--dns`:

```bash
docker run --dns 8.8.8.8 myapp
```

## 4. Optimasi Storage

### Gunakan Volume daripada Bind Mount

Volume umumnya menawarkan performa lebih baik dibanding bind mount:

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

### Pertimbangkan Menggunakan tmpfs Mount

Untuk data sementara, tmpfs mount dapat meningkatkan performa I/O:

```bash
docker run --tmpfs /tmp myapp
```

## 5. Logging dan Monitoring

### Gunakan Logging Driver JSON-file dengan Batas

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

### Implementasi Monitoring yang Tepat

Gunakan alat seperti Prometheus dan Grafana untuk monitoring komprehensif:

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

## 6. Optimasi Docker Daemon

### Sesuaikan Storage Driver

Pertimbangkan menggunakan overlay2 untuk performa lebih baik:

```json
{
  "storage-driver": "overlay2"
}
```

### Aktifkan Live Restore

Fitur ini memungkinkan container tetap berjalan meskipun Docker daemon tidak tersedia:

```json
{
  "live-restore": true
}
```

## 7. Optimasi di Level Aplikasi

### Gunakan Image Berbasis Alpine

Image berbasis Alpine biasanya lebih kecil dan lebih cepat diunduh:

```dockerfile
FROM alpine:3.14
RUN apk add --no-cache python3
```

### Optimalkan Kode Aplikasi Anda

Pastikan aplikasi Anda dioptimalkan untuk lingkungan container:
- Implementasi mekanisme caching yang baik
- Optimasi query database
- Gunakan pemrosesan asinkron jika memungkinkan

## 8. Benchmarking dan Profiling

### Gunakan Perintah Stats Bawaan Docker

```bash
docker stats
```

### Benchmark dengan Alat Seperti Apache Bench

```bash
ab -n 1000 -c 100 http://localhost/
```

## 9. Optimasi di Level Orkestrasi

Jika menggunakan alat orkestrasi seperti Kubernetes:

### Gunakan Horizontal Pod Autoscaler

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

### Implementasikan Liveness dan Readiness Probe yang Tepat

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
```

## Kesimpulan

Optimasi performa Docker adalah proses berkelanjutan yang melibatkan berbagai aspek dari setup Docker Anda, mulai dari pembuatan image hingga konfigurasi runtime dan optimasi di level aplikasi. Dengan menerapkan praktik terbaik ini dan terus memonitor lingkungan Docker Anda, Anda dapat secara signifikan meningkatkan performa dan efisiensi aplikasi yang tercontainerisasi.
