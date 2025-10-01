# Bab 2: Instalasi Docker

Menginstal Docker adalah langkah pertama dalam perjalanan Anda dengan containerisasi. Bab ini akan memandu Anda melalui proses instalasi Docker di berbagai sistem operasi, mengatasi masalah umum, dan memverifikasi instalasi Anda.

## Edisi Docker

Sebelum memulai, penting untuk memahami berbagai edisi Docker yang tersedia:

1. **Docker Engine - Community**: Platform Docker gratis dan open-source yang cocok untuk developer dan tim kecil.
2. **Docker Engine - Enterprise**: Dirancang untuk pengembangan dan tim IT perusahaan yang membangun, menjalankan, dan mengoperasikan aplikasi bisnis skala besar.
3. **Docker Desktop**: Aplikasi mudah dipasang untuk lingkungan Mac atau Windows yang mencakup Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, dan Credential Helper.

Untuk sebagian besar pengguna, Docker Engine - Community atau Docker Desktop sudah cukup.

## Instalasi Docker di Linux

Docker berjalan secara native di Linux, menjadikannya platform ideal untuk container Docker. Ada dua metode utama untuk menginstal Docker di Linux: menggunakan skrip kemudahan atau instalasi manual untuk distribusi tertentu.

### Metode 1: Menggunakan Skrip Instalasi Docker (Direkomendasikan untuk Setup Cepat)

Docker menyediakan skrip kemudahan yang secara otomatis mendeteksi distribusi Linux Anda dan menginstal Docker untuk Anda. Metode ini cepat dan bekerja di banyak distribusi Linux:

1. Jalankan perintah berikut untuk mengunduh dan mengeksekusi skrip instalasi Docker:
   ```
   wget -qO- https://get.docker.com | sh
   ```

2. Setelah instalasi selesai, mulai layanan Docker:
   ```
   sudo systemctl start docker
   ```

3. Aktifkan Docker agar berjalan saat boot:
   ```
   sudo systemctl enable docker
   ```

Metode ini ideal untuk setup cepat dan lingkungan pengujian. Namun, untuk lingkungan produksi, Anda mungkin ingin mempertimbangkan metode instalasi manual untuk kontrol lebih lanjut.

### Metode 2: Instalasi Manual untuk Distribusi Tertentu

Untuk kontrol lebih lanjut atas proses instalasi atau jika Anda ingin mengikuti langkah-langkah spesifik distribusi, Anda dapat menginstal Docker secara manual. Berikut adalah instruksi untuk distribusi Linux populer:

Docker berjalan secara native di Linux, menjadikannya platform ideal untuk container Docker. Berikut cara menginstal Docker di distribusi Linux populer:

### Ubuntu

1. Perbarui indeks paket Anda:
   ```
   sudo apt-get update
   ```

2. Instal prasyarat:
   ```
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

3. Tambahkan kunci GPG resmi Docker:
   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. Tambahkan repository stable:
   ```
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. Perbarui indeks paket lagi:
   ```
   sudo apt-get update
   ```

6. Instal Docker:
   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

### CentOS

1. Instal paket yang diperlukan:
   ```
   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. Tambahkan repository Docker:
   ```
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. Instal Docker:
   ```
   sudo yum install docker-ce docker-ce-cli containerd.io
   ```

4. Mulai dan aktifkan Docker:
   ```
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

### Distribusi Linux Lainnya

Untuk distribusi Linux lainnya, lihat dokumentasi resmi Docker: https://docs.docker.com/engine/install/

## Instalasi Docker di macOS

Untuk macOS, cara termudah menginstal Docker adalah dengan menggunakan Docker Desktop:

1. Unduh Docker Desktop untuk Mac dari situs resmi Docker: https://www.docker.com/products/docker-desktop

2. Klik dua kali file `.dmg` yang diunduh dan seret ikon Docker ke folder Applications Anda.

3. Buka Docker dari folder Applications Anda.

4. Ikuti instruksi di layar untuk menyelesaikan instalasi.

## Instalasi Docker di Windows

Untuk Windows 10 Pro, Enterprise, atau Education, Anda dapat menginstal Docker Desktop:

1. Unduh Docker Desktop untuk Windows dari situs resmi Docker: https://www.docker.com/products/docker-desktop

2. Klik dua kali installer untuk menjalankannya.

3. Ikuti wizard instalasi hingga selesai.

4. Setelah terinstal, Docker Desktop akan berjalan secara otomatis.

Untuk Windows 10 Home atau versi Windows yang lebih lama, Anda dapat menggunakan Docker Toolbox, yang menggunakan Oracle VirtualBox untuk menjalankan Docker:

1. Unduh Docker Toolbox dari: https://github.com/docker/toolbox/releases

2. Jalankan installer dan ikuti wizard instalasi.

3. Setelah terinstal, gunakan Docker Quickstart Terminal untuk berinteraksi dengan Docker.

## Langkah Setelah Instalasi

Setelah menginstal Docker, ada beberapa langkah yang sebaiknya Anda lakukan:

1. Verifikasi instalasi:
   ```
   docker version
   docker run hello-world
   ```

2. Konfigurasikan Docker agar berjalan saat boot (hanya Linux):
   ```
   sudo systemctl enable docker
   ```

3. Tambahkan user Anda ke grup docker agar dapat menjalankan perintah Docker tanpa sudo (hanya Linux):
   ```
   sudo usermod -aG docker $USER
   ```
   Catatan: Anda perlu logout dan login kembali agar perubahan ini berlaku.

## Docker Desktop vs Docker Engine

Penting untuk memahami perbedaan antara Docker Desktop dan Docker Engine:

- **Docker Desktop** adalah aplikasi yang ramah pengguna dan mencakup Docker Engine, Docker CLI client, Docker Compose, Docker Content Trust, Kubernetes, dan Credential Helper. Dirancang untuk instalasi dan penggunaan mudah di Mac dan Windows.

- **Docker Engine** adalah runtime inti Docker yang tersedia untuk sistem Linux. Tidak termasuk alat tambahan yang ada di Docker Desktop, namun dapat diinstal secara terpisah.

## Mengatasi Masalah Instalasi Umum

1. **Permission denied**: 
   Jika Anda mengalami error "permission denied", pastikan Anda telah menambahkan user Anda ke grup docker atau menggunakan sudo.

2. **Docker daemon tidak berjalan**: 
   Di Linux, coba mulai layanan Docker: `sudo systemctl start docker`

3. **Konflik dengan VirtualBox (Windows)**: 
   Pastikan Hyper-V diaktifkan untuk Docker Desktop, atau gunakan Docker Toolbox jika Anda masih membutuhkan VirtualBox.

4. **Sumber daya sistem tidak cukup**: 
   Docker Desktop membutuhkan minimal 4GB RAM. Tingkatkan alokasi RAM sistem atau virtual machine Anda jika diperlukan.

## Memperbarui Docker

Untuk memperbarui Docker:

- Di Linux, gunakan package manager Anda (misal, `apt-get upgrade docker-ce` di Ubuntu)
- Di Mac dan Windows, Docker Desktop akan memberi notifikasi update secara otomatis

## Menghapus Instalasi Docker

Jika Anda perlu menghapus Docker:

- Di Linux, gunakan package manager Anda (misal, `sudo apt-get purge docker-ce docker-ce-cli containerd.io` di Ubuntu)
- Di Mac, hapus Docker Desktop dari folder Applications
- Di Windows, uninstall Docker Desktop dari Control Panel

## Kesimpulan

Menginstal Docker umumnya merupakan proses yang mudah, namun dapat berbeda tergantung sistem operasi Anda. Selalu rujuk dokumentasi resmi Docker untuk instruksi instalasi terbaru sesuai sistem Anda. Dengan Docker berhasil diinstal, Anda siap menjelajahi dunia containerisasi!
