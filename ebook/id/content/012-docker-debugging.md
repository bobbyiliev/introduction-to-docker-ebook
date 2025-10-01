# Bab 12: Troubleshooting dan Debugging Docker

Meskipun sudah direncanakan dengan baik dan mengikuti praktik terbaik, masalah tetap bisa muncul saat bekerja dengan Docker. Bab ini membahas masalah umum yang mungkin Anda temui dan memberikan strategi untuk troubleshooting dan debugging yang efektif.

## 1. Masalah Siklus Hidup Container

### Container Tidak Mau Start

Jika container gagal dijalankan, gunakan perintah berikut:

```bash
# Lihat log container
docker logs <container_id>

# Inspeksi detail container
docker inspect <container_id>

# Cek status container
docker ps -a
```

### Container Langsung Keluar

Untuk container yang langsung keluar setelah dijalankan:

```bash
# Jalankan container dalam mode interaktif
docker run -it --entrypoint /bin/sh <image_name>

# Cek ENTRYPOINT dan CMD di Dockerfile
docker inspect --format='{{.Config.Entrypoint}}' <image_name>
docker inspect --format='{{.Config.Cmd}}' <image_name>
```

## 2. Masalah Jaringan

### Container Tidak Bisa Terhubung ke Jaringan

Untuk troubleshooting konektivitas jaringan:

```bash
# Inspeksi pengaturan jaringan
docker network inspect <network_name>

# Cek pengaturan jaringan container
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>

# Gunakan container debugging jaringan
docker run --net container:<container_id> nicolaka/netshoot
```

### Masalah Pemetaan Port

Jika Anda tidak bisa mengakses port yang diekspos container:

```bash
# Cek pemetaan port
docker port <container_id>

# Verifikasi pengaturan firewall host
sudo ufw status

# Tes port langsung di container
docker exec <container_id> nc -zv localhost <port>
```

## 3. Masalah Storage dan Volume

### Masalah Persistensi Data

Untuk masalah data yang tidak tersimpan:

```bash
# Lihat daftar volume
docker volume ls

# Inspeksi volume
docker volume inspect <volume_name>

# Cek mount volume di container
docker inspect --format='{{range .Mounts}}{{.Source}} -> {{.Destination}}{{"\n"}}{{end}}' <container_id>
```

### Masalah Ruang Disk

Jika kehabisan ruang disk:

```bash
# Cek penggunaan disk Docker
docker system df

# Hapus data yang tidak digunakan
docker system prune -a

# Identifikasi image berukuran besar
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -h
```

## 4. Masalah Resource

### Container Menggunakan CPU atau Memori Berlebihan

Untuk mengidentifikasi dan mengatasi masalah penggunaan resource:

```bash
# Monitor penggunaan resource
docker stats

# Atur batas resource
docker run --memory=512m --cpus=0.5 <image_name>

# Update batas untuk container yang sedang berjalan
docker update --cpus=0.75 <container_id>
```

## 5. Masalah Terkait Image

### Gagal Pull Image

Jika tidak bisa pull image:

```bash
# Cek status Docker Hub
curl -Is https://registry.hub.docker.com/v2/ | head -n 1

# Verifikasi login Docker Anda
docker login

# Coba pull dengan output verbose
docker pull --verbose <image_name>
```

### Gagal Build Image

Untuk masalah saat build image:

```bash
# Build dengan output verbose
docker build --progress=plain -t <image_name> .

# Cek masalah di Dockerfile
docker build --no-cache -t <image_name> .
```

## 6. Masalah Docker Daemon

### Docker Daemon Tidak Mau Start

Jika Docker daemon gagal dijalankan:

```bash
# Cek status Docker daemon
sudo systemctl status docker

# Lihat log Docker daemon
sudo journalctl -u docker.service

# Restart Docker daemon
sudo systemctl restart docker
```

## 7. Teknik Debugging

### Debugging Interaktif

Untuk debugging container yang sedang berjalan secara interaktif:

```bash
# Mulai shell interaktif di container yang berjalan
docker exec -it <container_id> /bin/bash

# Jalankan container baru dengan shell untuk debugging
docker run -it --entrypoint /bin/bash <image_name>
```

### Menggunakan Docker Events

Monitor event Docker untuk troubleshooting:

```bash
docker events
```

### Logging

Konfigurasi dan lihat log container:

```bash
# Lihat log container
docker logs <container_id>

# Ikuti output log
docker logs -f <container_id>

# Atur logging driver
docker run --log-driver json-file --log-opt max-size=10m <image_name>
```

## 8. Debugging Performa

### Identifikasi Bottleneck Performa

Gunakan perintah berikut untuk mengidentifikasi masalah performa:

```bash
# Monitor penggunaan resource container
docker stats

# Profil proses container
docker top <container_id>

# Gunakan cAdvisor untuk metrik lebih detail
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

## 9. Troubleshooting Docker Compose

Untuk masalah dengan Docker Compose:

```bash
# Lihat log semua service
docker-compose logs

# Rebuild dan recreate container
docker-compose up -d --build

# Cek konfigurasi
docker-compose config
```

## Kesimpulan

Troubleshooting dan debugging yang efektif adalah keterampilan penting saat bekerja dengan Docker. Dengan memahami teknik dan alat ini, Anda dapat dengan cepat mengidentifikasi dan menyelesaikan masalah di lingkungan Docker Anda. Selalu cek dokumentasi resmi Docker dan forum komunitas untuk informasi terbaru serta solusi masalah umum.
