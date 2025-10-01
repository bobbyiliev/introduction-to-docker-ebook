# Bab 5: Apa itu Dockerfile

Dockerfile adalah dokumen teks yang berisi serangkaian instruksi dan argumen. Instruksi-instruksi ini digunakan untuk membuat Docker image secara otomatis. Pada dasarnya, Dockerfile adalah skrip berisi perintah-perintah berurutan yang akan dijalankan Docker untuk merakit sebuah image, sehingga proses pembuatan image menjadi otomatis.

## Anatomi Dockerfile

Dockerfile biasanya terdiri dari komponen berikut:

1. Deklarasi base image
2. Informasi metadata dan label
3. Pengaturan environment
4. Operasi file dan direktori
5. Instalasi dependensi
6. Penyalinan kode aplikasi
7. Spesifikasi perintah eksekusi

Mari kita bahas lebih dalam setiap komponen dan instruksi yang digunakan.

## Instruksi Dockerfile

### FROM

Instruksi `FROM` menginisialisasi tahap build baru dan menetapkan base image untuk instruksi berikutnya.

```dockerfile
FROM ubuntu:20.04
```

Instruksi ini biasanya menjadi yang pertama dalam Dockerfile. Anda dapat menggunakan beberapa instruksi `FROM` dalam satu Dockerfile untuk multi-stage build.

### LABEL

`LABEL` menambahkan metadata ke image dalam format pasangan kunci-nilai.

```dockerfile
LABEL version="1.0" maintainer="john@example.com" description="Ini adalah contoh Docker image"
```

Label berguna untuk pengorganisasian image, informasi lisensi, anotasi, dan metadata lainnya.

### ENV

`ENV` menetapkan variabel environment dalam image.

```dockerfile
ENV APP_HOME=/app NODE_ENV=production
```

Variabel ini akan tetap ada saat container dijalankan dari image yang dihasilkan.

### WORKDIR

`WORKDIR` menetapkan direktori kerja untuk instruksi `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, dan `ADD` berikutnya.

```dockerfile
WORKDIR /app
```

Jika direktori belum ada, akan dibuat secara otomatis.

### COPY dan ADD

Instruksi `COPY` dan `ADD` digunakan untuk menyalin file dari host ke dalam image.

```dockerfile
COPY package.json .
ADD https://example.com/big.tar.xz /usr/src/things/
```

`COPY` umumnya lebih disukai karena lebih sederhana. `ADD` memiliki fitur tambahan seperti ekstraksi tar dan dukungan URL remote, namun dapat membuat perilaku build kurang terprediksi.

### RUN

`RUN` menjalankan perintah di layer baru di atas image saat ini dan menyimpan hasilnya.

```dockerfile
RUN apt-get update && apt-get install -y nodejs
```

Sebaiknya rantai perintah dengan `&&` dan lakukan pembersihan dalam satu instruksi `RUN` untuk menjaga layer tetap kecil.

### CMD

`CMD` menyediakan perintah default untuk container yang dijalankan. Hanya boleh ada satu instruksi `CMD` dalam Dockerfile.

```dockerfile
CMD ["node", "app.js"]
```

`CMD` dapat diganti saat runtime.

### ENTRYPOINT

`ENTRYPOINT` mengonfigurasi container agar berjalan sebagai executable.

```dockerfile
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

`ENTRYPOINT` sering digunakan bersama `CMD`, di mana `ENTRYPOINT` mendefinisikan executable dan `CMD` memberikan argumen default.

### EXPOSE

`EXPOSE` memberi tahu Docker bahwa container mendengarkan pada port jaringan tertentu saat runtime.

```dockerfile
EXPOSE 80 443
```

Instruksi ini tidak benar-benar mempublikasikan port; hanya berfungsi sebagai dokumentasi antara pembuat image dan pengguna container.

### VOLUME

`VOLUME` membuat mount point dan menandainya sebagai tempat volume eksternal dari host atau container lain.

```dockerfile
VOLUME /data
```

Instruksi ini berguna untuk bagian image yang bersifat mutable dan/atau dapat diubah oleh pengguna.

### ARG

`ARG` mendefinisikan variabel yang dapat diberikan pengguna saat build dengan perintah `docker build`.

```dockerfile
ARG VERSION=latest
```

Instruksi ini memungkinkan proses build image menjadi lebih fleksibel.

## Praktik Terbaik Menulis Dockerfile

1. **Gunakan multi-stage build**: Membantu membuat image akhir yang lebih kecil dengan memisahkan dependensi build dan runtime.

```dockerfile
FROM node:14 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

2. **Minimalkan jumlah layer**: Gabungkan perintah jika memungkinkan untuk mengurangi layer dan ukuran image.

3. **Manfaatkan cache build**: Urutkan instruksi dari yang paling jarang berubah ke yang paling sering berubah untuk memaksimalkan penggunaan cache.

4. **Gunakan `.dockerignore`**: Kecualikan file yang tidak relevan dengan build, mirip dengan `.gitignore`.

5. **Jangan instal paket yang tidak diperlukan**: Jaga image tetap ramping dan aman dengan hanya menginstal yang dibutuhkan.

6. **Gunakan tag spesifik**: Hindari tag `latest` untuk base image agar build lebih dapat direproduksi.

7. **Setel `WORKDIR`**: Selalu gunakan `WORKDIR` daripada instruksi seperti `RUN cd … && do-something`.

8. **Gunakan `COPY` daripada `ADD`**: Kecuali Anda memang membutuhkan fitur tambahan dari `ADD`, gunakan `COPY` untuk transparansi.

9. **Gunakan variabel environment**: Terutama untuk versi dan path, agar Dockerfile lebih fleksibel.

## Konsep Lanjutan Dockerfile

### Health Check

Anda dapat menggunakan instruksi `HEALTHCHECK` untuk memberi tahu Docker cara menguji container agar tetap berjalan dengan baik.

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s CMD curl -f http://localhost/ || exit 1
```

### Bentuk Shell dan Exec

Banyak instruksi Dockerfile dapat ditulis dalam bentuk shell atau exec:

- Bentuk shell: `RUN apt-get install python3`
- Bentuk exec: `RUN ["apt-get", "install", "python3"]`

Bentuk exec lebih disukai karena lebih eksplisit dan menghindari masalah dengan string shell.

### BuildKit

BuildKit adalah backend baru untuk build Docker yang menawarkan performa, manajemen storage, dan fitur yang lebih baik. Anda dapat mengaktifkannya dengan environment variable:

```bash
export DOCKER_BUILDKIT=1
```

## Kesimpulan

Dockerfile adalah alat yang sangat kuat untuk membuat Docker image yang dapat direproduksi dan dikontrol versinya. Dengan menguasai instruksi dan praktik terbaik Dockerfile, Anda dapat membuat aplikasi yang efisien, aman, dan portabel. Ingat, menulis Dockerfile yang baik adalah proses iteratif – terus perbaiki Dockerfile Anda seiring Anda memahami kebutuhan aplikasi dan kemampuan Docker.
