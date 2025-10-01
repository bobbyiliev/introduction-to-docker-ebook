# Bab 1: Pengenalan Docker

## Apa itu Docker?

Docker adalah platform open-source yang mengotomatisasi proses deployment, scaling, dan manajemen aplikasi menggunakan teknologi containerisasi. Docker memungkinkan developer untuk mengemas aplikasi beserta dependensinya ke dalam unit standar yang disebut container, yang dapat berjalan secara konsisten di berbagai lingkungan.

### Konsep Utama:

1. **Containerisasi**: Bentuk virtualisasi ringan yang mengemas aplikasi dan dependensinya bersama-sama.
2. **Docker Engine**: Runtime yang memungkinkan Anda membangun dan menjalankan container.
3. **Docker Image**: Template read-only yang digunakan untuk membuat container.
4. **Docker Container**: Instance yang dapat dijalankan dari Docker image.
5. **Docker Hub**: Registry berbasis cloud untuk menyimpan dan berbagi Docker image.

## Mengapa Menggunakan Docker?

Docker menawarkan banyak keuntungan bagi developer dan tim operasi:

1. **Konsistensi**: Memastikan aplikasi berjalan dengan cara yang sama di lingkungan pengembangan, pengujian, dan produksi.
2. **Isolasi**: Container terisolasi satu sama lain dan dari sistem host, meningkatkan keamanan dan mengurangi konflik.
3. **Portabilitas**: Container dapat berjalan di sistem mana pun yang mendukung Docker, tanpa memandang infrastruktur yang digunakan.
4. **Efisiensi**: Container berbagi kernel OS host, sehingga lebih ringan dibandingkan mesin virtual tradisional.
5. **Skalabilitas**: Mudah melakukan skala aplikasi secara horizontal dengan menjalankan banyak container.
6. **Kontrol Versi**: Docker image dapat diberi versi, sehingga memudahkan rollback dan update.

## Arsitektur Docker

Docker menggunakan arsitektur client-server:

1. **Docker Client**: Cara utama pengguna berinteraksi dengan Docker melalui command line interface (CLI).
2. **Docker Host**: Mesin yang menjalankan Docker daemon (dockerd).
3. **Docker Daemon**: Mengelola objek Docker seperti image, container, network, dan volume.
4. **Docker Registry**: Menyimpan Docker image (misal: Docker Hub).

Berikut diagram sederhana arsitektur Docker:

```
┌─────────────┐     ┌─────────────────────────────────────┐
│ Docker CLI  │     │            Docker Host              │
│ (docker)    │◄───►│  ┌────────────┐      ┌───────────┐  │
└─────────────┘     │  │   Docker   │      │ Containers│  │
                    │  │   Daemon   │◄────►│    and    │  │
                    │  │  (dockerd) │      │  Images   │  │
                    │  └────────────┘      └───────────┘  │
                    └─────────────────────────────────────┘
                               ▲
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Docker Registry   │
                    │    (Docker Hub)     │
                    └─────────────────────┘
```

## Container vs. Mesin Virtual

Meskipun container dan mesin virtual (VM) digunakan untuk mengisolasi aplikasi, keduanya memiliki beberapa perbedaan utama:

| Aspek           | Container                              | Mesin Virtual                        |
|-----------------|----------------------------------------|--------------------------------------|
| OS              | Berbagi kernel OS host                 | Menjalankan OS dan kernel penuh      |
| Penggunaan Resource | Ringan, overhead minimal           | Penggunaan resource lebih tinggi     |
| Waktu Boot      | Detik                                  | Menit                                |
| Isolasi         | Isolasi tingkat proses                 | Isolasi penuh                        |
| Portabilitas    | Sangat portabel di berbagai OS         | Kurang portabel, tergantung OS       |
| Performa        | Performa mendekati native              | Ada sedikit overhead performa        |
| Penyimpanan     | Biasanya lebih kecil (MB)              | Lebih besar (GB)                     |

## Alur Kerja Dasar Docker

1. **Build**: Buat Dockerfile yang mendefinisikan aplikasi dan dependensinya.
2. **Ship**: Push Docker image ke registry seperti Docker Hub.
3. **Run**: Pull image dan jalankan sebagai container di host yang mendukung Docker.

Berikut contoh sederhana alur kerja ini:

```bash
# Build image
docker build -t myapp:v1 .

# Kirim image ke Docker Hub
docker push username/myapp:v1

# Jalankan container
docker run -d -p 8080:80 username/myapp:v1
```

## Komponen Docker

1. **Dockerfile**: File teks berisi instruksi untuk membangun Docker image.
2. **Docker Compose**: Alat untuk mendefinisikan dan menjalankan aplikasi Docker multi-container.
3. **Docker Swarm**: Solusi clustering dan orkestrasi native dari Docker.
4. **Docker Network**: Memfasilitasi komunikasi antar container Docker.
5. **Docker Volume**: Menyediakan penyimpanan persisten untuk data container.

## Use Case Docker

1. **Arsitektur Microservices**: Deploy dan skala layanan secara independen.
2. **Continuous Integration/Continuous Deployment (CI/CD)**: Mempercepat proses pengembangan dan deployment.
3. **Lingkungan Pengembangan**: Membuat lingkungan pengembangan yang konsisten di seluruh tim.
4. **Isolasi Aplikasi**: Menjalankan beberapa versi aplikasi di host yang sama.
5. **Migrasi Aplikasi Legacy**: Containerisasi aplikasi lama agar lebih mudah dikelola dan di-deploy.

## Kesimpulan

Docker telah merevolusi cara aplikasi dikembangkan, dikirim, dan dijalankan. Dengan menyediakan cara standar untuk mengemas dan mendistribusikan aplikasi, Docker mengatasi banyak tantangan dalam pengembangan dan operasi perangkat lunak modern. Sepanjang buku ini, kita akan membahas lebih dalam setiap aspek Docker, memberikan pengetahuan dan keterampilan agar Anda dapat memanfaatkan teknologi ini secara efektif.
