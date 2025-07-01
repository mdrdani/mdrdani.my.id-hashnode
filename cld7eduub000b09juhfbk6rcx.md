---
title: "Deploy Laravel App to Amazon Lightsail Container (Part 2)"
seoTitle: "Deploy Laravel App to Amazon Lightsail Container (Part 2)"
seoDescription: "Deploy Laravel App to Amazon Lightsail Container (Part 2)"
datePublished: Wed Dec 14 2022 13:12:34 GMT+0000 (Coordinated Universal Time)
cuid: cld7eduub000b09juhfbk6rcx
slug: deploy-laravel-app-to-amazon-lightsail-container-part-2
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Y5yxdx2a4PI/upload/01f5439e554b14f773f48f5ed2fa0ed8.jpeg
tags: linux, docker, aws, containers

---

Masuk Ke **AWS Console**, lalu search **Lightsail**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392324637/da9f73a9-f5c3-4eca-9d11-7ada4669da9b.webp align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392344381/8ab0757a-931c-45d8-9c3b-015465836c5d.webp align="center")

Gambar di atas tampilan dashboard A**mazon Lightsail**, yang pertama kita lakukan adalah membuat **database** nya terlebih dahulu lalu ke **containers.**

Masuk ke tab **Databases** lalu create database**.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392368956/190c64a7-49e3-4a49-b8f0-afc301ca0d45.webp align="center")

pilih database location karna di sini belum ada location Indonesia-Jakarta maka kita pilih yang terdekat **Singapore (ap-southeast-1).**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392387320/e375f308-1716-4e4f-90c3-e242bbc3d36e.webp align="center")

Sesuaikan kebutuhan database nya pake apa, kalau sekarang pilih saja **MySQL versi 5.7.39.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392409169/22e79525-d473-4f0f-9d8e-7b92ee7ff75d.webp align="center")

Buat Username.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392427081/dee9c522-d4cb-48f6-9fa7-63bc65831e35.webp align="center")

Pilih database plan, pilih aja yang paling terkecil dahulu.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392452028/be53d09b-a270-4697-8006-ea0959184bd2.webp align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392478366/43688855-51c5-4e14-878f-6323da19e15d.webp align="center")

Tunggu kurang lebih 2 menit sampai database tersedia. Selanjut nya untuk meremote database sementara **enable** database menjadi **public mode.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392499471/f1b5d4cf-78b1-4548-a8ab-4990b9b34332.webp align="center")

dan remote menggunakan **MysqlWorkbench** atau **HeidiSQL.** Jika sudah di atur **disabled** kembali untuk keamanan.

Lanjut konfigurasi **containers.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392519673/52e69a18-6ef6-4533-91bb-54b48f5e0fd5.webp align="center")

create container service

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392549200/8909801e-976b-47e5-a632-cf2eede8f2e3.webp align="center")

Pilih container service location di mana, di sini belum tersedia **jakarta** jadi pilih yang lebih dekat yaitu **singapore**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392578868/4fbb2db6-2d9a-40f7-9429-3c4960fd2121.webp align="center")

Untuk awal memilih container service capacity kita pilih yang paling rendah saja. scroll ke bawah pilih create container service.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392616057/91ed355d-3dac-47c4-b05e-d3c6a74fe6c3.webp align="center")

Tunggu proses sampai containers tersedia kurang lebih 2 menit. Jika sudah tersedia bisa masuk ke bagian **deployment.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392639127/0e01803e-f812-4312-8ce5-25418383dd0f.webp align="center")

buat **container name**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392662824/a588c2a1-18f7-4918-92b3-196a9340707b.webp align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392713854/af2913f1-2935-41c6-8263-66801637a8d1.webp align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392756457/60fc67bc-6da3-40ca-9ce8-8d2da0cc3c42.webp align="center")

Sesuaikan variable environment seperti di atas. Jika sudah S**ave and deploy.** Tunggu proses deploy dan boom …..

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674392810755/3f5db33f-32fd-47f6-a19c-05e1aa551a5d.webp align="center")