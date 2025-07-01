---
title: "Docker Fundamental - Sesi 3"
seoTitle: "Docker Fundamental- Sesi 3"
seoDescription: "Docker Fundamental- Sesi 3"
datePublished: Wed Jan 25 2023 06:21:41 GMT+0000 (Coordinated Universal Time)
cuid: cldba3gn004q0a0nv53v01ww1
slug: docker-fundamental-sesi-3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1674612101147/05df81ae-e1e3-4b97-acd2-da74feecf88a.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1674627037034/ce447ae7-0171-4d85-b63b-85866494edee.png
tags: docker, mysql, mariadb

---

Pada Sesi 1 dan Sesi 2 kita sudah belajar bagaimana dockerize aplikasi golang hello world,running aplikasi dan mencoba push image ke docker hub registry.

Selanjut nya pada Sesi 3 ini kita coba praktek Run Docker image Database.

### Database Docker Image Mysql/MariaDB

[MariaDB Official Image](https://hub.docker.com/_/mariadb) sudah menyiapkan image docker Mariadb yang bisa langsung di pakai dan di jalankan tanpa perlu repot kita menyiapkan dari install server baru, install depedencies, dll.

untuk mendownload image yang sudah ada di Docker Hub Registry dapat menggunakan perintah **docker pull**

```bash
$ docker pull mariadb
```

jika sudah terdownload image mariadb, kita akan merunning sebagai container. namun kita sebaiknya melihat dokumentasi [How to use this image](https://hub.docker.com/_/mariadb) menggunakan **environment variable** apa saja yang tersedia.

```bash
$ docker run -itd --name mariadb-1 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -p 3306:3306 mariadb
```

container yang di jalankan di beri nama **mariadb-1** dan menggunakan **environment variable password diperbolehkan kosong** dan di expose ke **port default mysql/mariadb 3306**. jika saat run container terjadi error tidak berjalan, coba selidiki dengan melihat dari container lognya.

```bash
$ docker logs mariadb-1
```

mari kita coba connect ke database mariadb, di sini saya menggunakan Aplikasi [Heidisql](https://www.heidisql.com/), temen2 bisa juga menggunakan [Mysql Workbench](https://dev.mysql.com/downloads/workbench/) atau yang lain nya.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674616584594/950c4700-5123-4a64-b573-6668fc1a7af0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674616612012/695b8bdc-0815-43df-b3b4-9c6b58f57d4a.png align="center")

oke sukses connect ke database nya.

Ada satu hal yang perlu di ingat bahwa jika kita hapus container **mariadb-1** maka database yang sudah kita buat tadi akan hilang. gak percaya? coba aja delete container **mariadb-1** dan create container ulang lagi dengan nama yang sama **mariadb-1.**

```bash
$ docker container rm mariadb-1
$ docker run -itd --name mariadb-1 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -p 3306:3306 mariadb
```

akan jadi masalah besar jika database sudah banyak dibuat terhapus. Bagaimana cara nya jika container terhapus namun database tidak terhapus?? jawaban nya yaitu menggunakan **<mark>Docker Volume</mark>.**

### Docker Volume/Storage Bind Mounts Save Data dari Container ke Host

untuk menggunakan Docker Volume kita hanya menambahkan **flag -v.**

buat dahulu folder/path tempat menyimpan data container. Di sini saya buat di **WSL Ubuntu Windows** dengan nama folder **storedata-mysql.**

```bash
$ docker run -itd --name mariadb-1 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -v /home/sdb/storedata-mysql:/var/lib/mysql -p 3306:3306 mariadb
```

pada folder storedata-mysql akan terisi file dan folder seperti ini

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674623234600/e0964c97-fef9-4163-bf4a-73cd79c841c3.png align="center")

coba lagi create **database** dan **table,** selanjutnya coba remove container dan create running kembali container dengan nama yang berbeda namun di arahkan ke docker volume **storedata-mysql**.

```bash
$ docker container stop mariadb-1
$ docker container rm mariadb-1
$ docker run -itd --name mariadb-2 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -v /home/sdb/storedata-mysql:/var/lib/mysql -p 3306:3306 mariadb
```

connect kembali menggunakan Heidisql.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674625436706/31464036-e5e3-4c3e-90a3-c77348a59ac1.png align="center")

database dan table masih tersedia dengan aman.

### SSH Remote Ke Container

Cara untuk remote ke container yaitu ketahui **container id** atau **nama container** terlebih dahulu dan untuk mengakses nya menggunakan command **docker exec.**

```bash
$ docker container ls
$ docker exec -it 4b90 sh
```