# Apa itu Docker Swarm mode

Menurut dokumentasi resmi **Docker**, swarm adalah sekelompok mesin yang menjalankan **Docker** dan bergabung dalam sebuah cluster. Jika Anda menjalankan **Docker swarm**, perintah Anda akan dieksekusi pada cluster oleh swarm manager. Mesin dalam swarm bisa berupa fisik maupun virtual. Setelah bergabung ke swarm, mereka disebut sebagai node. Saya akan melakukan demo singkat di akun **DigitalOcean** saya!

**Docker Swarm** terdiri dari **manager node** dan **worker node**.

Manager node mendistribusikan tugas ke worker node, sedangkan worker node hanya menjalankan tugas tersebut. Untuk High Availability, disarankan memiliki **3** atau **5** manager node.

## Docker Services

Untuk mendepoy image aplikasi saat Docker Engine dalam mode swarm, Anda harus membuat sebuah service. Service adalah sekumpulan container dari `image:tag` yang sama. Service memudahkan Anda melakukan scaling aplikasi.

Untuk memiliki **Docker services**, Anda harus memiliki **Docker swarm** dan node yang siap.

![](https://cdn.devdojo.com/posts/images/May2020/services-diagram.jpg)

## Membangun Swarm

Saya akan demo singkat cara membangun **Docker swarm dengan 3 manager dan 3 worker**.

Untuk itu saya akan deploy 6 droplet di DigitalOcean:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm.png)

Setelah siap, **install docker** seperti pada **[Introduction to Docker Part 1](https://devdojo.com/tutorials/introduction-to-docker-part-1)** lalu ikuti langkah berikut:

### Langkah 1

Inisialisasi docker swarm di manager node pertama Anda:

```
docker swarm init --advertise-addr your_droplet_ip_here
```

### Langkah 2

Untuk mendapatkan perintah join manager lainnya, jalankan:

```
docker swarm join-token manager
```

> Catatan: Ini akan memberikan perintah yang perlu dijalankan di node manager swarm lainnya. Contoh:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm-join-managers-bobby-iliev.png)

### Langkah 3

Untuk mendapatkan perintah join worker, jalankan:

```
docker swarm join-token worker
```

Perintah untuk worker mirip dengan manager, hanya tokennya berbeda.

Output saat join manager akan terlihat seperti ini:

![](https://cdn.devdojo.com/posts/images/May2020/docker-join-manager.png)

### Langkah 4

Setelah Anda punya perintah join, **ssh ke node lain dan join** sebagai worker dan manager sesuai kebutuhan.

# Mengelola cluster

Setelah menjalankan perintah join di semua worker dan manager, untuk melihat status cluster gunakan perintah berikut:

* Untuk melihat semua node yang tersedia:

```
docker node ls
```

> Catatan: Perintah ini hanya bisa dijalankan dari **swarm manager**! Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-node-ls.png)

* Untuk melihat informasi status saat ini:

```
docker info
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-info-bobby-iliev.png)

## Promosi worker menjadi manager

Untuk mempromosikan worker menjadi manager jalankan dari salah satu manager node:

```
docker node promote node_id_here
```

Perlu diketahui setiap manager juga bertindak sebagai worker, jadi dari output docker info Anda akan melihat 6 worker dan 3 manager node.

## Menggunakan Services

Untuk membuat service gunakan perintah berikut:

```
docker service create --name bobby-web -p 80:80 --replicas 5 bobbyiliev/php-apache
```

Image bobbyiliev/php-apache sudah saya push ke Docker hub seperti dijelaskan di blog sebelumnya.

Untuk melihat daftar service Anda:

```
docker service ls
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-create-service.png)

Untuk melihat daftar container yang berjalan gunakan:

```
docker services ps name_of_your_service_here
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-service-ps.png)

Kemudian Anda bisa mengunjungi IP address salah satu node dan melihat service! Kita bisa mengunjungi node mana saja dari swarm dan tetap mendapatkan service.

## Scaling service

Anda bisa mencoba mematikan salah satu node dan melihat bagaimana swarm secara otomatis menjalankan proses baru di node lain agar tetap sesuai dengan jumlah replica yang diinginkan.

Untuk melakukannya, buka control panel **DigitalOcean** dan matikan salah satu Droplet. Lalu kembali ke terminal dan jalankan:

```
docker services ps name_of_your_service_here
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-replicas.png)

Pada screenshot di atas, saya mematikan droplet bernama worker-2 dan replica bobby-web.2 langsung dijalankan di node lain bernama worker-01 agar tetap sesuai dengan 5 replica yang diinginkan.

Untuk menambah replica jalankan:

```
docker service scale name_of_your_service_here=7
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-more-replicas.png)

Ini akan otomatis menjalankan 2 container tambahan, Anda bisa cek dengan perintah docker service ps:

```
docker service ps name_of_your_service_here
```

Sebagai tes, coba hidupkan kembali node yang tadi dimatikan dan cek apakah mengambil tugas baru?

**Tips**: Menambah node baru ke cluster tidak otomatis mendistribusikan tugas yang sedang berjalan.

## Menghapus service

Untuk menghapus service, jalankan perintah berikut:

```
docker service rm name_of_your_service
```

Output:

![](https://cdn.devdojo.com/posts/images/May2020/docker-delete-service.png)

Sekarang Anda tahu cara inisialisasi dan scaling cluster docker swarm! Untuk informasi lebih lanjut silakan baca dokumentasi resmi Docker [di sini](https://docs.docker.com/engine/swarm/).

## Tes Pengetahuan Docker Swarm

Setelah membaca bab ini, pastikan untuk menguji pengetahuan Anda dengan **[Quiz Docker Swarm](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)**:

[https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)
