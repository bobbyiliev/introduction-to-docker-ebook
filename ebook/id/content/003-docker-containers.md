# Bab 3: Bekerja dengan Docker Container

Docker container adalah paket ringan, mandiri, dan dapat dieksekusi yang berisi semua yang dibutuhkan untuk menjalankan sebuah perangkat lunak, termasuk kode, runtime, alat sistem, pustaka, dan pengaturan. Pada bab ini, kita akan mempelajari cara bekerja dengan Docker container secara efektif.

## Menjalankan Container Pertama Anda

Mari mulai dengan menjalankan sebuah container sederhana:

```bash
docker run hello-world
```

Perintah ini melakukan hal berikut:
1. Memeriksa apakah image `hello-world` tersedia secara lokal
2. Jika tidak ditemukan, mengunduh image dari Docker Hub
3. Membuat container dari image tersebut
4. Menjalankan container, yang akan mencetak pesan hello
5. Keluar dari container

## Perintah Dasar Docker

Berikut beberapa perintah Docker penting untuk bekerja dengan container:

### Melihat Daftar Container

Untuk melihat semua container yang sedang berjalan:
```bash
docker ps
```

Untuk melihat semua container (termasuk yang sudah berhenti):
```bash
docker ps -a
```

### Memulai dan Menghentikan Container

Untuk menghentikan container yang sedang berjalan:
```bash
docker stop <container_id_or_name>
```

Untuk memulai container yang sudah berhenti:
```bash
docker start <container_id_or_name>
```

Untuk me-restart container:
```bash
docker restart <container_id_or_name>
```

### Menghapus Container

Untuk menghapus container yang sudah berhenti:
```bash
docker rm <container_id_or_name>
```

Untuk memaksa menghapus container yang sedang berjalan:
```bash
docker rm -f <container_id_or_name>
```

## Menjalankan Container dalam Berbagai Mode

### Mode Detached

Menjalankan container di latar belakang:
```bash
docker run -d <image_name>
```

### Mode Interaktif

Menjalankan container dan berinteraksi dengannya:
```bash
docker run -it <image_name> /bin/bash
```

## Pemetaan Port

Untuk memetakan port container ke host:
```bash
docker run -p <host_port>:<container_port> <image_name>
```

Contoh:
```bash
docker run -d -p 80:80 nginx
```

## Melihat Log Container

Melihat log container:
```bash
docker logs <container_id_or_name>
```

Mengikuti log container secara real-time:
```bash
docker logs -f <container_id_or_name>
```

## Menjalankan Perintah di Container yang Berjalan

Untuk menjalankan perintah di container yang sedang berjalan:
```bash
docker exec -it <container_id_or_name> <command>
```

Contoh:
```bash
docker exec -it my_container /bin/bash
```

## Contoh Praktis: Menjalankan Container Apache

Mari kita jalankan container web server Apache:

1. Unduh image:
   ```bash
   docker pull httpd
   ```

2. Jalankan container:
   ```bash
   docker run -d --name my-apache -p 8080:80 httpd
   ```

3. Verifikasi container berjalan:
   ```bash
   docker ps
   ```

4. Akses halaman default dengan membuka browser dan menuju ke `http://localhost:8080`

5. Ubah halaman default:
   ```bash
   docker exec -it my-apache /bin/bash
   echo "<h1>Hello dari container Apache saya!</h1>" > /usr/local/apache2/htdocs/index.html
   exit
   ```

6. Segarkan browser Anda untuk melihat perubahan

## Manajemen Resource Container

### Membatasi Memori

Menjalankan container dengan batas memori:
```bash
docker run -d --memory=512m <image_name>
```

### Membatasi CPU

Menjalankan container dengan batas CPU:
```bash
docker run -d --cpus=0.5 <image_name>
```

## Jaringan Container

### Melihat Daftar Jaringan

```bash
docker network ls
```

### Membuat Jaringan

```bash
docker network create my_network
```

### Menghubungkan Container ke Jaringan

```bash
docker run -d --network my_network --name my_container <image_name>
```

## Persistensi Data dengan Volume

### Membuat Volume

```bash
docker volume create my_volume
```

### Menjalankan Container dengan Volume

```bash
docker run -d -v my_volume:/path/in/container <image_name>
```

## Pemeriksaan Kesehatan Container

Docker menyediakan kemampuan pemeriksaan kesehatan bawaan. Anda dapat mendefinisikan health check di Dockerfile:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

## Membersihkan

Menghapus semua container yang sudah berhenti:
```bash
docker container prune
```

Menghapus semua resource yang tidak digunakan (container, jaringan, image):
```bash
docker system prune
```

## Kesimpulan

Bekerja dengan Docker container melibatkan berbagai operasi mulai dari menjalankan dan menghentikan hingga topik lanjutan seperti manajemen resource dan jaringan. Seiring Anda semakin terbiasa dengan operasi-operasi ini, Anda akan dapat memanfaatkan potensi penuh Docker dalam alur kerja pengembangan dan deployment Anda.

Ingat, container didesain untuk bersifat sementara. Selalu simpan data penting di volume atau gunakan mekanisme persistensi yang sesuai untuk aplikasi Anda.
