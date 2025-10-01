# Bab 13: Konsep dan Fitur Docker Lanjutan

Saat Anda semakin mahir menggunakan Docker, Anda akan menemukan konsep dan fitur yang lebih canggih. Bab ini membahas beberapa topik tersebut untuk membantu Anda meningkatkan keterampilan Docker, meskipun ini di luar cakupan ebook pengantar ini.

## 1. Multi-stage Build

Multi-stage build memungkinkan Anda membuat Dockerfile yang lebih efisien dengan menggunakan beberapa pernyataan FROM dalam Dockerfile Anda.

```dockerfile
# Tahap build
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Tahap akhir
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
CMD ["./main"]
```

Pendekatan ini mengurangi ukuran image akhir dengan hanya menyertakan artefak yang diperlukan dari tahap build.

## 2. Docker BuildKit

BuildKit adalah engine build generasi berikutnya untuk Docker. Aktifkan dengan mengatur environment variable:

```bash
export DOCKER_BUILDKIT=1
```

BuildKit menawarkan build lebih cepat, manajemen cache lebih baik, dan fitur canggih seperti:

- Resolusi dependency secara bersamaan
- Caching instruksi yang efisien
- Garbage collection otomatis

## 3. Jaringan Bridge Kustom

Buat lingkungan jaringan terisolasi untuk container Anda:

```bash
docker network create --driver bridge isolated_network
docker run --network=isolated_network --name container1 -d nginx
docker run --network=isolated_network --name container2 -d nginx
```

Container di jaringan ini dapat saling berkomunikasi menggunakan nama sebagai hostname.

## 4. Docker Context

Kelola beberapa lingkungan Docker dengan context:

```bash
# Buat context baru
docker context create my-remote --docker "host=ssh://user@remote-host"

# Lihat daftar context
docker context ls

# Beralih context
docker context use my-remote
```

## 5. Docker Content Trust (DCT)

DCT menyediakan cara untuk memverifikasi integritas dan publisher image:

```bash
# Aktifkan DCT
export DOCKER_CONTENT_TRUST=1

# Push image yang sudah ditandatangani
docker push myrepo/myimage:latest
```

## 6. Docker Secrets

Kelola data sensitif dengan Docker secrets:

```bash
# Buat secret
echo "mypassword" | docker secret create my_secret -

# Gunakan secret dalam service
docker service create --name myservice --secret my_secret myimage
```

## 7. Docker Health Check

Implementasikan health check kustom di Dockerfile Anda:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

## 8. Docker Plugin

Perluas fungsionalitas Docker dengan plugin:

```bash
# Install plugin
docker plugin install vieux/sshfs

# Gunakan plugin
docker volume create -d vieux/sshfs -o sshcmd=user@host:/path sshvolume
```

## 9. Fitur Eksperimental Docker

Aktifkan fitur eksperimental di konfigurasi Docker daemon (`/etc/docker/daemon.json`):

```json
{
  "experimental": true
}
```

Ini membuka fitur seperti:
- Checkpoint dan restore
- Rootless mode

## 10. Perlindungan Container Escape

Gunakan opsi keamanan untuk mencegah container escape:

```bash
docker run --security-opt="no-new-privileges:true" --cap-drop=ALL myimage
```

## 11. Instruksi Dockerfile Kustom

Buat instruksi Dockerfile kustom menggunakan ONBUILD:

```dockerfile
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
```

## 12. Docker Manifest

Buat dan push image multi-arsitektur:

```bash
docker manifest create myrepo/myimage myrepo/myimage:amd64 myrepo/myimage:arm64
docker manifest push myrepo/myimage
```

## 13. Docker Buildx

Buildx adalah plugin CLI yang memperluas perintah docker build dengan dukungan penuh fitur BuildKit:

```bash
# Buat builder instance baru
docker buildx create --name mybuilder

# Build dan push image multi-platform
docker buildx build --platform linux/amd64,linux/arm64 -t myrepo/myimage:latest --push .
```

## 14. Docker Compose Profile

Gunakan profile di Docker Compose untuk menjalankan service secara selektif:

```yaml
services:
  frontend:
    image: frontend
    profiles: ["frontend"]
  backend:
    image: backend
    profiles: ["backend"]
```

Jalankan profile tertentu:

```bash
docker-compose --profile frontend up -d
```

## Kesimpulan

Konsep dan fitur Docker lanjutan ini menyediakan alat yang sangat berguna untuk mengoptimalkan workflow Docker, meningkatkan keamanan, dan memperluas kemampuan Docker. Dengan menerapkan teknik-teknik ini ke proyek Anda, Anda dapat menciptakan lingkungan Docker yang lebih efisien, aman, dan fleksibel.
