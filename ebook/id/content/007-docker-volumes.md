# Bab 7: Docker Volumes

Docker volume adalah mekanisme utama untuk menyimpan data yang dihasilkan dan digunakan oleh container Docker. Meskipun container dapat membuat, memperbarui, dan menghapus file, perubahan tersebut akan hilang ketika container dihapus dan semua perubahan hanya berlaku untuk container tersebut. Volume memberikan kemampuan untuk menghubungkan path filesystem tertentu dari container ke mesin host. Jika sebuah direktori di container di-mount, perubahan pada direktori tersebut juga terlihat di host. Jika kita mount direktori yang sama pada container yang dijalankan ulang, kita akan melihat file yang sama.

## Mengapa Menggunakan Docker Volumes?

1. **Persistensi Data**: Volume memungkinkan Anda menyimpan data meskipun container dihentikan atau dihapus.
2. **Berbagi Data**: Volume dapat dibagikan dan digunakan ulang oleh beberapa container.
3. **Performa**: Volume disimpan di filesystem host, yang umumnya memberikan performa I/O lebih baik, terutama untuk database.
4. **Manajemen Data**: Volume memudahkan backup, restore, dan migrasi data.
5. **Pemisahan**: Volume memisahkan konfigurasi host Docker dari runtime container.

## Jenis Docker Volumes

### 1. Named Volumes

Named volume adalah cara yang direkomendasikan untuk menyimpan data di Docker. Volume ini dibuat secara eksplisit dan diberi nama.

Membuat named volume:
```bash
docker volume create my_volume
```

Menggunakan named volume:
```bash
docker run -d --name devtest -v my_volume:/app nginx:latest
```

### 2. Anonymous Volumes

Anonymous volume dibuat otomatis oleh Docker dan diberi nama acak. Cocok untuk data sementara yang tidak perlu dipertahankan setelah container dihapus.

Menggunakan anonymous volume:
```bash
docker run -d --name devtest -v /app nginx:latest
```

### 3. Bind Mounts

Bind mount memetakan path spesifik dari mesin host ke path di container. Cocok untuk lingkungan pengembangan.

Menggunakan bind mount:
```bash
docker run -d --name devtest -v /path/on/host:/app nginx:latest
```

## Bekerja dengan Docker Volumes

### Melihat Daftar Volume

Untuk melihat semua volume:
```bash
docker volume ls
```

### Memeriksa Volume

Untuk mendapatkan informasi detail tentang sebuah volume:
```bash
docker volume inspect my_volume
```

### Menghapus Volume

Untuk menghapus volume tertentu:
```bash
docker volume rm my_volume
```

Untuk menghapus semua volume yang tidak digunakan:
```bash
docker volume prune
```

### Backup Volume

Untuk backup volume:
```bash
docker run --rm -v my_volume:/source -v /path/on/host:/backup ubuntu tar cvf /backup/backup.tar /source
```

### Restore Volume

Untuk restore volume dari backup:
```bash
docker run --rm -v my_volume:/target -v /path/on/host:/backup ubuntu tar xvf /backup/backup.tar -C /target --strip 1
```

## Volume Driver

Docker mendukung volume driver, yang memungkinkan Anda menyimpan volume di host remote atau cloud provider, dan lainnya.

Beberapa driver volume populer:
- Local (default)
- NFS
- AWS EBS
- Azure File Storage

Menggunakan driver volume tertentu:
```bash
docker volume create --driver <driver_name> my_volume
```

## Praktik Terbaik Menggunakan Docker Volumes

1. **Gunakan named volume**: Lebih mudah dikelola dan dilacak daripada anonymous volume.

2. **Jangan gunakan bind mount di produksi**: Kurang portabel dan berpotensi menimbulkan risiko keamanan.

3. **Gunakan volume untuk database**: Database membutuhkan storage yang persisten dan mendapat manfaat dari performa volume.

4. **Perhatikan permission**: Pastikan proses di container memiliki permission yang cukup untuk membaca/menulis ke volume.

5. **Bersihkan volume yang tidak digunakan**: Gunakan `docker volume prune` secara berkala untuk menghapus volume yang tidak digunakan dan menghemat ruang.

6. **Gunakan label volume**: Label membantu mengorganisasi dan mengelola volume.
   ```bash
   docker volume create --label project=myapp my_volume
   ```

7. **Pertimbangkan menggunakan Docker Compose**: Docker Compose memudahkan manajemen volume di banyak container.

## Konsep Lanjutan Volume

### 1. Volume Read-Only

Anda dapat mount volume sebagai read-only agar container tidak bisa mengubah data:
```bash
docker run -d --name devtest -v my_volume:/app:ro nginx:latest
```

### 2. Tmpfs Mount

Tmpfs mount disimpan hanya di memori host, cocok untuk data sensitif:
```bash
docker run -d --name tmptest --tmpfs /app nginx:latest
```

### 3. Berbagi Volume Antar Container

Anda dapat berbagi volume antar beberapa container:
```bash
docker run -d --name container1 -v my_volume:/app nginx:latest
docker run -d --name container2 -v my_volume:/app nginx:latest
```

### 4. Volume Plugin

Docker mendukung plugin volume pihak ketiga untuk fitur tambahan:
```bash
docker plugin install <plugin_name>
docker volume create -d <plugin_name> my_volume
```

## Troubleshooting Volume

1. **Volume tidak menyimpan data**: Pastikan Anda menggunakan nama volume dan path mount yang benar.

2. **Masalah permission**: Periksa permission direktori yang di-mount di host dan di container.

3. **Volume tidak bisa dihapus**: Pastikan tidak ada container yang menggunakan volume sebelum menghapusnya.

4. **Masalah performa**: Jika I/O lambat, pertimbangkan menggunakan driver volume yang dioptimalkan untuk kebutuhan Anda.

## Kesimpulan

Docker volume adalah komponen penting untuk manajemen data di lingkungan Docker. Volume menyediakan cara yang fleksibel dan efisien untuk menyimpan dan berbagi data antara container dan sistem host. Dengan memahami cara membuat, mengelola, dan menggunakan volume secara efektif, Anda dapat membangun aplikasi container yang lebih robust dan mudah dipelihara.

Ingat, pilihan antara berbagai jenis volume (named volume, bind mount, atau tmpfs mount) tergantung pada use case Anda. Selalu pertimbangkan kebutuhan persistensi, performa, dan keamanan saat bekerja dengan Docker volume.
