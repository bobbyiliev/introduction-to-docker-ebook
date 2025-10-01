# Bab 9: Praktik Terbaik Keamanan Docker

Keamanan adalah aspek krusial dalam bekerja dengan Docker, terutama di lingkungan produksi. Bab ini akan membahas praktik keamanan penting untuk membantu Anda membangun dan menjaga lingkungan Docker yang aman.

## 1. Selalu Perbarui Docker

Gunakan selalu versi Docker terbaru untuk mendapatkan patch keamanan terkini.

```bash
sudo apt-get update
sudo apt-get upgrade docker-ce
```

## 2. Gunakan Image Resmi

Sebisa mungkin, gunakan image resmi dari Docker Hub atau sumber terpercaya. Image ini secara rutin diperbarui dan dipindai kerentanannya.

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest  # Image Nginx resmi
```

## 3. Pindai Image dari Kerentanan

Gunakan alat seperti Docker Scout atau Trivy untuk memindai image dari kerentanan yang diketahui.

```bash
docker scout cve <image_name>
```

## 4. Batasi Resource Container

Cegah serangan Denial of Service dengan membatasi resource container:

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
```

## 5. Gunakan User Non-Root

Jalankan container sebagai user non-root untuk membatasi dampak jika terjadi pelanggaran keamanan pada container:

```dockerfile
FROM node:14
RUN groupadd -r myapp && useradd -r -g myapp myuser
USER myuser
```

## 6. Gunakan Manajemen Secret

Untuk data sensitif seperti password dan API key, gunakan Docker secrets:

```bash
echo "mysecretpassword" | docker secret create db_password -
```

Kemudian di docker-compose.yml Anda:

```yaml
version: '3.8'
services:
  db:
    image: mysql
    secrets:
      - db_password
secrets:
  db_password:
    external: true
```

## 7. Aktifkan Content Trust

Tandatangani dan verifikasi tag image:

```bash
export DOCKER_CONTENT_TRUST=1
docker push myrepo/myimage:latest
```

## 8. Gunakan Container Read-Only

Jika memungkinkan, jalankan container dalam mode read-only:

```yaml
version: '3.8'
services:
  web:
    image: nginx
    read_only: true
    tmpfs:
      - /tmp
      - /var/cache/nginx
```

## 9. Terapkan Segmentasi Jaringan

Gunakan jaringan Docker untuk mengisolasi container:

```yaml
version: '3.8'
services:
  frontend:
    networks:
      - frontend
  backend:
    networks:
      - backend
networks:
  frontend:
  backend:
```

## 10. Audit Keamanan Secara Berkala

Lakukan audit rutin pada lingkungan Docker Anda menggunakan alat seperti Docker Bench for Security:

```bash
docker run -it --net host --pid host --userns host --cap-add audit_control \
    -e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
    -v /var/lib:/var/lib \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /usr/lib/systemd:/usr/lib/systemd \
    -v /etc:/etc --label docker_bench_security \
    docker/docker-bench-security
```

## 11. Gunakan Security-Enhanced Linux (SELinux) atau AppArmor

Keduanya memberikan lapisan keamanan tambahan. Pastikan sudah diaktifkan dan dikonfigurasi dengan benar di sistem host Anda.

## 12. Implementasi Logging dan Monitoring

Gunakan kemampuan logging Docker dan pertimbangkan integrasi dengan alat monitoring eksternal:

```yaml
version: '3.8'
services:
  web:
    image: nginx
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
```

## Kesimpulan

Menerapkan praktik keamanan ini akan secara signifikan meningkatkan keamanan lingkungan Docker Anda. Ingat, keamanan adalah proses berkelanjutan, dan penting untuk selalu mengikuti perkembangan ancaman serta fitur keamanan Docker terbaru.
