# Bab 4: Apa itu Docker Image

Docker image adalah paket ringan, mandiri, dan dapat dieksekusi yang berisi semua yang dibutuhkan untuk menjalankan sebuah perangkat lunak, termasuk kode, runtime, alat sistem, pustaka, dan pengaturan. Image adalah fondasi dari Docker container.

## Konsep Utama

1. **Layer**: Image terdiri dari beberapa layer, masing-masing merepresentasikan perubahan pada filesystem.
2. **Base Image**: Fondasi dari sebuah image, biasanya berupa sistem operasi minimal.
3. **Parent Image**: Image yang menjadi dasar pembuatan image Anda.
4. **Image Tag**: Label yang digunakan untuk versi dan identifikasi image.
5. **Image ID**: Identitas unik untuk setiap image.

## Bekerja dengan Docker Image

### Melihat Daftar Image

Untuk melihat semua image di sistem lokal Anda:

```bash
docker images
```

Atau gunakan perintah yang lebih detail:

```bash
docker image ls
```

### Mengunduh Image dari Docker Hub

Untuk mengunduh image dari Docker Hub:

```bash
docker pull <image_name>:<tag>
```

Contoh:

```bash
docker pull ubuntu:20.04
```

Jika tidak ada tag yang ditentukan, Docker akan mengambil tag `latest` secara default.

### Menjalankan Container dari Image

Untuk menjalankan container dari sebuah image:

```bash
docker run <image_name>:<tag>
```

Contoh:

```bash
docker run -it ubuntu:20.04 /bin/bash
```

### Informasi Image

Untuk mendapatkan informasi detail tentang sebuah image:

```bash
docker inspect <image_name>:<tag>
```

### Menghapus Image

Untuk menghapus sebuah image:

```bash
docker rmi <image_name>:<tag>
```

atau

```bash
docker image rm <image_name>:<tag>
```

Untuk menghapus semua image yang tidak digunakan:

```bash
docker image prune
```

## Membuat Image Kustom

### Menggunakan Dockerfile

1. Buat file bernama `Dockerfile` tanpa ekstensi.
2. Definisikan instruksi untuk membangun image Anda.

Contoh Dockerfile:

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

### Membuat dari Container yang Berjalan

1. Lakukan perubahan pada container yang sedang berjalan.
2. Buat image baru dari container tersebut:

```bash
docker commit <container_id> my-new-image:tag
```

## Tagging Image

Untuk memberi tag pada image yang sudah ada:

```bash
docker tag <source_image>:<tag> <target_image>:<tag>
```

Contoh:

```bash
docker tag my-nginx:v1 my-dockerhub-username/my-nginx:v1
```

## Push Image ke Docker Hub

1. Login ke Docker Hub:

```bash
docker login
```

2. Push image:

```bash
docker push my-dockerhub-username/my-nginx:v1
```

## Layer Image dan Caching

Memahami layer sangat penting untuk mengoptimalkan proses build image:

1. Setiap instruksi di Dockerfile membuat layer baru.
2. Layer di-cache dan digunakan ulang pada build berikutnya.
3. Urutkan instruksi dari yang paling jarang berubah ke yang paling sering berubah untuk mempercepat build.

Contoh pemanfaatan caching:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY ./static-files /var/www/html
COPY ./config-files /etc/nginx
```

## Multi-stage Build

Multi-stage build memungkinkan Anda menggunakan beberapa FROM dalam Dockerfile. Ini berguna untuk membuat image produksi yang lebih kecil.

Contoh:

```dockerfile
# Tahap build
FROM golang:1.16 AS build
WORKDIR /app
COPY . .
RUN go build -o myapp

# Tahap produksi
FROM alpine:3.14
COPY --from=build /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

## Pemindaian dan Keamanan Image

Docker menyediakan fitur pemindaian image bawaan:

```bash
docker scan <image_name>:<tag>
```

Ini membantu mengidentifikasi kerentanan pada image Anda.

## Praktik Terbaik dalam Bekerja dengan Image

1. Gunakan tag spesifik, hindari `latest` untuk reproducibility.
2. Buat image sekecil mungkin dengan base image minimal dan multi-stage build.
3. Gunakan `.dockerignore` untuk mengecualikan file yang tidak diperlukan dari konteks build.
4. Manfaatkan cache build dengan mengurutkan instruksi Dockerfile secara efektif.
5. Rutin update base image untuk mendapatkan patch keamanan.
6. Scan image untuk kerentanan sebelum deployment.

## Manajemen dan Pembersihan Image

Untuk mengelola ruang disk, bersihkan image yang tidak digunakan secara berkala:

```bash
docker system prune -a
```

Perintah ini menghapus semua image yang tidak digunakan, bukan hanya yang dangling.

## Kesimpulan

Docker image adalah konsep fundamental dalam containerisasi. Image menyediakan cara yang konsisten dan portabel untuk mengemas aplikasi beserta dependensinya. Dengan menguasai pembuatan, optimasi, dan manajemen image, Anda dapat meningkatkan workflow Docker dan deployment aplikasi secara signifikan.
