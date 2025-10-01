# Bab 8: Docker Compose

Docker Compose adalah alat yang sangat kuat untuk mendefinisikan dan menjalankan aplikasi Docker multi-container. Dengan Compose, Anda menggunakan file YAML untuk mengonfigurasi layanan, jaringan, dan volume aplikasi Anda. Kemudian, dengan satu perintah, Anda dapat membuat dan menjalankan semua layanan dari konfigurasi tersebut.

> **Catatan**: Docker Compose sekarang terintegrasi ke dalam Docker CLI. Perintah baru adalah `docker compose` (tanpa tanda hubung). Kita akan menggunakan perintah baru ini di seluruh bab ini.

## Keunggulan Utama Docker Compose

1. **Kesederhanaan**: Mendefinisikan seluruh stack aplikasi dalam satu file.
2. **Reproducibility**: Mudah berbagi dan mengontrol versi konfigurasi aplikasi Anda.
3. **Skalabilitas**: Perintah sederhana untuk menambah atau mengurangi jumlah layanan.
4. **Konsistensi Lingkungan**: Memastikan lingkungan pengembangan, staging, dan produksi identik.
5. **Peningkatan Workflow**: Compose dapat digunakan sepanjang siklus pengembangan untuk pengujian, staging, dan produksi.

## File docker-compose.yml

File `docker-compose.yml` adalah inti dari Docker Compose. File ini mendefinisikan semua komponen dan konfigurasi aplikasi Anda. Berikut contoh dasar:

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```

Penjelasan contoh di atas:

- `version`: Menentukan versi format file Compose.
- `services`: Mendefinisikan container yang membentuk aplikasi Anda.
- `web`: Layanan yang dibangun dari Dockerfile di direktori saat ini.
- `redis`: Layanan yang menggunakan image Redis publik.

## Konsep Utama Docker Compose

1. **Services**: Container yang membentuk aplikasi Anda.
2. **Networks**: Cara layanan Anda saling berkomunikasi.
3. **Volumes**: Tempat layanan Anda menyimpan dan mengakses data.

## Perintah Dasar Docker Compose

- `docker compose up`: Membuat dan menjalankan container
  ```bash
  docker compose up -d  # Jalankan dalam mode detached
  ```

- `docker compose down`: Menghentikan dan menghapus container, jaringan, image, dan volume
  ```bash
  docker compose down --volumes  # Sekaligus hapus volume
  ```

- `docker compose ps`: Melihat daftar container
- `docker compose logs`: Melihat output dari container
  ```bash
  docker compose logs -f web  # Ikuti log untuk layanan web
  ```

## Fitur Lanjutan Docker Compose

### 1. Variabel Environment

Anda dapat menggunakan file .env atau menetapkan langsung di file compose:

```yaml
version: '3.8'
services:
  web:
    image: "webapp:${TAG}"
    environment:
      - DEBUG=1
```

### 2. Extending Services

Gunakan `extends` untuk berbagi konfigurasi umum:

```yaml
version: '3.8'
services:
  web:
    extends:
      file: common-services.yml
      service: webapp
```

### 3. Healthchecks

Pastikan layanan siap sebelum memulai layanan yang bergantung padanya:

```yaml
version: '3.8'
services:
  web:
    image: "webapp:latest"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
```


## Contoh Praktis

### Contoh 1: WordPress dengan MySQL

```yaml
version: '3.8'
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress

volumes:
  db_data: {}
```

Penjelasan detail:

1. **Version**:
   `version: '3.8'` menandakan versi format file Compose. Versi 3.8 kompatibel dengan Docker Engine 19.03.0+.

2. **Services**:
   Terdapat dua layanan: `db` dan `wordpress`.

   a. **Layanan db**:
   - `image: mysql:5.7`: Menggunakan image MySQL 5.7 resmi.
   - `volumes`: Membuat volume bernama `db_data` dan mount ke `/var/lib/mysql` di container. Data database tetap ada meski container dihapus.
   - `restart: always`: Container akan selalu restart jika berhenti.
   - `environment`: Mengatur variabel environment MySQL:
     - `MYSQL_ROOT_PASSWORD`: Password root MySQL.
     - `MYSQL_DATABASE`: Membuat database bernama "wordpress".
     - `MYSQL_USER` dan `MYSQL_PASSWORD`: Membuat user baru dengan password yang ditentukan.

   b. **Layanan wordpress**:
   - `depends_on`: Memastikan layanan `db` dijalankan sebelum `wordpress`.
   - `image: wordpress:latest`: Menggunakan image WordPress resmi terbaru.
   - `ports`: Memetakan port 8000 host ke port 80 container.
   - `restart: always`: Container akan selalu restart jika berhenti.
   - `environment`: Mengatur variabel environment WordPress:
     - `WORDPRESS_DB_HOST`: Host database, menggunakan nama layanan `db:3306`.
     - `WORDPRESS_DB_USER`, `WORDPRESS_DB_PASSWORD`, `WORDPRESS_DB_NAME`: Sesuai pengaturan di layanan db.

3. **Volumes**:
   `db_data: {}`: Membuat volume bernama yang dikelola Docker untuk data MySQL.

Cara menjalankan setup ini:

1. Simpan YAML di atas sebagai `docker-compose.yml`.
2. Di direktori yang sama, jalankan `docker compose up -d`.
3. Setelah container berjalan, akses WordPress di `http://localhost:8000` melalui browser.

Setup ini menyediakan lingkungan WordPress lengkap dengan database MySQL, siap digunakan. Penggunaan variabel environment dan volume memastikan setup fleksibel dan data tetap persisten.

### Contoh 2: Aplikasi Flask dengan Redis dan Nginx

```yaml
version: '3.8'
services:
  flask:
    build: ./flask
    environment:
      - FLASK_ENV=development
    volumes:
      - ./flask:/code

  redis:
    image: "redis:alpine"

  nginx:
    image: "nginx:alpine"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    ports:
      - "80:80"
    depends_on:
      - flask

networks:
  frontend:
  backend:

volumes:
  db-data:
```

Penjelasan:

1. **Version**: 
   Menggunakan versi 3.8 format Compose.

2. **Services**:
   Terdapat tiga layanan: `flask`, `redis`, dan `nginx`.

   a. **Layanan flask**:
   - `build: ./flask`: Docker akan membangun image dari Dockerfile di direktori `./flask`.
   - `environment`: Mengatur `FLASK_ENV=development` untuk mode debug Flask.
   - `volumes`: Mount direktori lokal `./flask` ke `/code` di container, memudahkan pengembangan.

   b. **Layanan redis**:
   - `image: "redis:alpine"`: Menggunakan image Redis berbasis Alpine Linux yang ringan.

   c. **Layanan nginx**:
   - `image: "nginx:alpine"`: Menggunakan image Nginx berbasis Alpine Linux.
   - `volumes`: Mount file lokal `nginx.conf` ke `/etc/nginx/nginx.conf` di container (read-only).
   - `ports`: Memetakan port 80 host ke port 80 container.
   - `depends_on`: Memastikan layanan `flask` dijalankan sebelum Nginx.

3. **Networks**:
   Mendefinisikan dua jaringan: `frontend` dan `backend`. Ini memungkinkan isolasi layanan, misal Nginx dan Flask di frontend, Flask dan Redis di backend.

4. **Volumes**:
   `db-data`: Membuat volume bernama, siap digunakan jika nanti menambah layanan database.

Cara menggunakan setup ini:

1. Siapkan aplikasi Flask di direktori `flask` beserta Dockerfile-nya.
2. Siapkan file `nginx.conf` di direktori yang sama dengan `docker-compose.yml`.
3. Jalankan `docker compose up -d` untuk memulai layanan.

Konfigurasi ini membangun server aplikasi Flask, Redis untuk cache atau message broker, dan Nginx sebagai reverse proxy. Kode Flask di-mount sebagai volume untuk kemudahan pengembangan. Nginx menangani request dan meneruskannya ke Flask.

Penggunaan image berbasis Alpine untuk Redis dan Nginx menjaga ukuran image tetap kecil, bermanfaat untuk deployment dan scaling.

Setup ini sangat cocok untuk pengembangan dan pengujian aplikasi Flask dalam lingkungan yang mirip produksi, dengan web server (Nginx) di depan aplikasi (Flask) dan sistem cache/pesan (Redis).

## Praktik Terbaik Docker Compose

1. Gunakan version control untuk file docker-compose.yml Anda.
2. Jaga agar lingkungan pengembangan, staging, dan produksi semirip mungkin.
3. Gunakan build argument dan variabel environment untuk fleksibilitas.
4. Manfaatkan healthcheck agar dependensi layanan terpenuhi.
5. Gunakan file `.env` untuk variabel environment spesifik.
6. Optimalkan image agar tetap kecil dan efisien.
7. Gunakan docker-compose.override.yml untuk pengaturan pengembangan lokal.

## Skalabilitas Layanan

Docker Compose dapat menambah jumlah layanan dengan satu perintah:

```bash
docker compose up -d --scale web=3
```

Perintah ini akan menjalankan 3 instance dari layanan `web`.

## Jaringan di Docker Compose

Secara default, Compose membuat satu jaringan untuk aplikasi Anda. Setiap container layanan bergabung ke jaringan default dan dapat diakses oleh container lain di jaringan tersebut, serta dapat ditemukan dengan hostname sesuai nama layanan.

Anda juga dapat menentukan jaringan kustom:

```yaml
version: '3.8'
services:
  web:
    networks:
      - frontend
      - backend
  db:
    networks:
      - backend

networks:
  frontend:
  backend:
```

## Volume di Docker Compose

Compose memungkinkan Anda membuat volume bernama yang dapat digunakan ulang oleh beberapa layanan:

```yaml
version: '3.8'
services:
  db:
    image: postgres
    volumes:
      - data:/var/lib/postgresql/data

volumes:
  data:
```

## Kesimpulan

Docker Compose menyederhanakan proses manajemen aplikasi multi-container, menjadikannya alat penting bagi developer yang bekerja dengan Docker. Dengan menguasai Docker Compose, Anda dapat mempercepat workflow pengembangan, memastikan konsistensi lintas lingkungan, dan mudah mengelola aplikasi kompleks dengan banyak layanan yang saling terhubung.

Ingat, selalu gunakan perintah `docker compose` terbaru, bukan `docker-compose`, karena kini sudah terintegrasi langsung ke Docker CLI dan menawarkan fungsionalitas serta performa yang lebih baik.
