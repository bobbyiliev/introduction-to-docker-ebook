# Bab 6: Jaringan Docker

Jaringan Docker memungkinkan container berkomunikasi satu sama lain dan dengan dunia luar. Ini adalah aspek penting dari Docker yang memungkinkan pembuatan aplikasi multi-container yang kompleks dan arsitektur microservices.

## Driver Jaringan Docker

Docker menggunakan arsitektur plug-in untuk jaringan, menawarkan beberapa driver jaringan bawaan:

1. **Bridge**: Driver jaringan default. Cocok untuk container mandiri yang perlu berkomunikasi.
2. **Host**: Menghilangkan isolasi jaringan antara container dan host Docker.
3. **Overlay**: Memungkinkan komunikasi antar container di beberapa host Docker daemon.
4. **MacVLAN**: Memberikan alamat MAC ke container, sehingga tampak seperti perangkat fisik di jaringan.
5. **None**: Menonaktifkan semua jaringan untuk container.
6. **Network plugins**: Memungkinkan penggunaan driver jaringan pihak ketiga.

## Bekerja dengan Jaringan Docker

### Melihat Daftar Jaringan

Untuk melihat semua jaringan:

```bash
docker network ls
```

Perintah ini menampilkan ID jaringan, nama, driver, dan cakupan untuk setiap jaringan.

### Memeriksa Jaringan

Untuk mendapatkan informasi detail tentang sebuah jaringan:

```bash
docker network inspect <network_name>
```

Ini memberikan informasi seperti subnet jaringan, gateway, container yang terhubung, dan opsi konfigurasi.

### Membuat Jaringan

Untuk membuat jaringan baru:

```bash
docker network create --driver <driver> <network_name>
```

Contoh:

```bash
docker network create --driver bridge my_custom_network
```

Anda dapat menentukan opsi tambahan seperti subnet, gateway, rentang IP, dll.:

```bash
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 my_custom_network
```

### Menghubungkan Container ke Jaringan

Saat menjalankan container, Anda dapat menentukan jaringan yang akan digunakan:

```bash
docker run --network <network_name> <image>
```

Contoh:

```bash
docker run --network my_custom_network --name container1 -d nginx
```

Anda juga dapat menghubungkan container yang sedang berjalan ke jaringan:

```bash
docker network connect <network_name> <container_name>
```

### Memutuskan Container dari Jaringan

Untuk memutuskan container dari jaringan:

```bash
docker network disconnect <network_name> <container_name>
```

### Menghapus Jaringan

Untuk menghapus jaringan:

```bash
docker network rm <network_name>
```

## Penjelasan Mendalam tentang Driver Jaringan

### Jaringan Bridge

Jaringan bridge adalah tipe jaringan yang paling umum digunakan di Docker. Cocok untuk container yang berjalan di host Docker daemon yang sama.

Poin penting tentang jaringan bridge:

- Setiap container yang terhubung ke jaringan bridge mendapatkan alamat IP unik.
- Container pada jaringan bridge yang sama dapat berkomunikasi menggunakan alamat IP.
- Jaringan bridge default memiliki beberapa keterbatasan, jadi lebih baik membuat jaringan bridge kustom.

Contoh membuat dan menggunakan jaringan bridge kustom:

```bash
docker network create my_bridge
docker run --network my_bridge --name container1 -d nginx
docker run --network my_bridge --name container2 -d nginx
```

Sekarang `container1` dan `container2` dapat berkomunikasi menggunakan nama container sebagai hostname.

### Jaringan Host

Jaringan host menempatkan container pada stack jaringan host. Ini menawarkan performa jaringan terbaik namun mengorbankan isolasi jaringan.

Contoh:

```bash
docker run --network host -d nginx
```

Dalam kasus ini, jika container membuka port 80, maka akan langsung dapat diakses pada port 80 mesin host.

### Jaringan Overlay

Jaringan overlay digunakan dalam mode Docker Swarm untuk memungkinkan komunikasi antar container di beberapa host Docker daemon.

Untuk membuat jaringan overlay:

```bash
docker network create --driver overlay my_overlay
```

Kemudian, saat membuat service di mode swarm, Anda dapat menghubungkannya ke jaringan ini:

```bash
docker service create --network my_overlay --name my_service nginx
```

### Jaringan MacVLAN

Jaringan MacVLAN memungkinkan Anda memberikan alamat MAC ke container, sehingga tampak seperti perangkat fisik di jaringan Anda.

Contoh:

```bash
docker network create -d macvlan \
  --subnet=192.168.0.0/24 \
  --gateway=192.168.0.1 \
  -o parent=eth0 my_macvlan_net
```

Kemudian jalankan container pada jaringan ini:

```bash
docker run --network my_macvlan_net -d nginx
```

## Troubleshooting Jaringan

1. **Komunikasi antar Container**: 
   Gunakan perintah `docker exec` untuk masuk ke container dan gunakan alat seperti `ping`, `curl`, atau `wget` untuk menguji konektivitas.

2. **Inspeksi Jaringan**: 
   Gunakan `docker network inspect` untuk melihat informasi detail tentang jaringan.

3. **Pemetaan Port**: 
   Gunakan `docker port <container>` untuk melihat pemetaan port container.

4. **Masalah DNS**: 
   Periksa file `/etc/resolv.conf` di dalam container untuk memverifikasi pengaturan DNS.

5. **Network Namespace**: 
   Untuk troubleshooting lanjutan, Anda dapat masuk ke network namespace container:
   ```bash
   pid=$(docker inspect -f '{{.State.Pid}}' <container_name>)
   nsenter -t $pid -n ip addr
   ```

## Praktik Terbaik

1. Gunakan jaringan bridge kustom daripada jaringan bridge default untuk isolasi lebih baik dan resolusi DNS bawaan.
2. Gunakan jaringan overlay untuk komunikasi antar host di mode swarm.
3. Gunakan jaringan host hanya jika benar-benar membutuhkan performa tinggi.
4. Berhati-hatilah saat membuka port, hanya buka yang diperlukan saja.
5. Gunakan Docker Compose untuk mengelola aplikasi multi-container dan jaringannya.

## Topik Lanjutan

### Enkripsi Jaringan

Untuk jaringan overlay, Anda dapat mengaktifkan enkripsi untuk mengamankan trafik antar container:

```bash
docker network create --opt encrypted --driver overlay my_secure_network
```

### Plugin Jaringan

Docker mendukung plugin jaringan pihak ketiga. Pilihan populer termasuk Weave Net, Calico, dan Flannel. Plugin ini dapat menyediakan fitur tambahan seperti routing lanjutan, kebijakan jaringan, dan enkripsi.

### Service Discovery

Docker menyediakan service discovery bawaan untuk container pada jaringan yang sama. Container dapat saling mengakses menggunakan nama container sebagai hostname. Pada mode swarm, terdapat load balancing bawaan untuk service.

## Kesimpulan

Jaringan adalah komponen penting dari Docker yang memungkinkan aplikasi terdistribusi yang kompleks. Dengan memahami dan menggunakan kemampuan jaringan Docker secara efektif, Anda dapat membuat aplikasi container yang aman, efisien, dan skalabel. Selalu pertimbangkan use case spesifik Anda saat memilih driver dan konfigurasi jaringan.
