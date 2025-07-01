---
title: "Deploy Laravel App to Amazon Lightsail Container (Part 1)"
seoTitle: "Deploy Laravel App to Amazon Lightsail Container (Part 1)"
seoDescription: "Deploy Laravel App to Amazon Lightsail Container (Part 1)"
datePublished: Tue Dec 13 2022 12:49:05 GMT+0000 (Coordinated Universal Time)
cuid: cld7dnags00030ajx4hoahr06
slug: deploy-laravel-app-to-amazon-lightsail-container-part-1
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/oXlXu2qukGE/upload/8fd3994da3f8b97fcb4a9e34a159b3a0.jpeg
tags: laravel, aws, containers, aws-lightsail

---

Hallo Guys, kali ini akan sharing step by step bagaimana deploy aplikasi Laravel yang telah kita buat ke AWS menggunakan Amazon Lightsail Container.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674388792698/80c91a08-e486-4559-94dc-6fd3569e1237.webp align="center")

Gambar diatas contoh topologi Standard Laravel Deployment. Permasalahan pertama pada gambar di atas aplikasi Laravel berjalan hanya di 1 server kita akan susah untuk scalling jika aplikasi Laravel yang telah kita buat akan di akses oleh user yang terus bertambah. Biasa nya yang akan bertambah yaitu Database dan Session Files. Selain itu permasalahan kedua environment kita terkadang berbeda dari local di deploy ke server publish yang mengakibatkan aplikasi tidak dapat berjalan, Nah untuk mengatasi permasalahan tersebut kita bisa deploy aplikasi laravel yang berisikan (Code, Os Package, Runtime, Libraries) di bungkus menggunakan container image Docker ke AWS menggunakan **Amazon Lightsail Container**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674388813385/1c6da2c4-b9ca-4eb0-b5bd-268bdfb3a041.webp align="center")

Mari kita coba jalankandi local dahulu sebelum di deploy ke Amazon Lightsail.

Requirement :

1. Docker (docker desktop for windows)
    
2. Akun AWS.
    
3. Akun Docker Hub.
    
4. Apache 2
    
5. PHP 7.4
    
6. Composer 2.0
    
7. mysql-server 5.7
    

Pertama kita persiapkan dahulu aplikasi Laravel. Di sini saya menggunakan aplikasi Laravel yang saya clone dari github saya [https://github.com/mdrdani/MarketPlace](https://github.com/mdrdani/MarketPlace).

kita buat file Dockerfile.local

```bash
FROM public.ecr.aws/docker/library/php:7.4-apache
RUN docker-php-ext-install bcmath pdo pdo_mysql
```

Kita coba build docker image untuk local development dahulu.

```bash
$ docker build -t php-mysql-ext:7.4-apache -f Dockerfile.local .
```

Check Image yang sudah di build.

```bash
$ docker images
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674388928518/a67d2835-63a8-47de-ac35-546b64c9a607.webp align="center")

Selanjutnya masuk ke directory aplikasi **Laravel**. Lakukan composer install via docker.

```bash
$ docker run --rm -it -v ${pwd}:/app public.ecr.aws/docker/library/composer:2.0 composer install --no-dev
```

check version php.

```bash
$ docker run --rm -it -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php --version
```

selanjut nya kita copy environment nya ke 2 file local dan production.

```bash
$ cp .\.env.example .env.local
$ cp .\.env.example .env.prod
```

kita pasang key generate nya di environment **local** dengan docker.

```bash
$ docker run --rm -it -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php artisan --env=local key:generate
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674389008595/f6f33bba-6b6d-498e-be94-5fcb596f05b5.webp align="center")

setelah itu kan kita akan migration dan memerlukan database, di sini kita akan mengambil **docker image mysql-server** terlebih dahulu.

```bash
$ docker pull mysql/mysql-server:5.7
```

dan kita membuat **network agar antar container dapat terhubung** dengan command

```bash
$ docker network create -d bridge my_network
$ docker network ls
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674389053168/f3dc7f49-d98e-4032-835b-8d97d9b03b6a.webp align="center")

jika sudah terbentuk **network** selanjut nya kita akan hubungkan **container mysql-server** dengan **container php** dengan cara kita create dahulu container mysql-server.

```bash
$ docker run -d --name mysqldock -e MYSQL_ROOT_HOST=% -e MYSQL_ROOT_PASSWORD=password -v mysql:/var/lib/mysql --network my_network mysql/mysql-server:5.7
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674391451491/ace07200-05df-46e0-b516-10a72872b837.webp align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674391472626/ad34ca0f-fa33-4e1b-8307-172278bbc23e.webp align="center")

command di atas **password root bisa di ganti terserah** semaunya, dan untuk **nama juga terserah**, jika sudah jadi container mysql-server **jangan lupa create database nya**, setelah itu jalankan migration dengan docker. Perhatikan di bagian DB\_HOST di situ samakan dengan yang ada di docker container.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674391489644/8ce72cf0-e728-4b50-a056-31b9f7042b79.webp align="center")

```bash
$ docker run --network my_network --rm -it -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php artisan --env=local migrate:refresh --seed
```

hubungkan juga storage:links.

```bash
$ docker run --network my_network --rm -it -p 8089:8000 -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php artisan --env=local storage:link
```

dan coba run dengan mengakses [http://localhost:8080](http://localhost:8080).

```bash
$ docker run --network my_network --rm -it -p 8080:8000 -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php artisan --env=local serve --host=0.0.0.0
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674391546115/623be9fa-252b-423d-ba00-6a73bf7c2391.webp align="center")

﻿﻿﻿Sampai di sini percobaan di local menggunakan docker sudah selesai. next kita akan publish ke **Amazon lightsail**.

**buat file Dockerfile**

```bash
FROM php:7.4.32

RUN apt-get update -y && apt-get install -y openssl zip unzip git
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN docker-php-ext-install bcmath pdo pdo_mysql
WORKDIR /app
COPY . /app
RUN composer install --no-dev --optimize-autoloader --no-interaction
RUN php artisan config:cache && \
php artisan route:cache && \
php artisan view:cache && \
php artisan storage:link && \
chmod 0777 -R /app/storage
CMD php artisan serve --host=0.0.0.0 --port=8181
EXPOSE 8181
```

Buat generate key untuk environment production

```bash
$ docker run --rm -it -v ${pwd}:/var/www/html php-mysql-ext:7.4-apache php artisan key:generate
```

selanjut nya kita build menjadi docker image dan siap kita push ke docker hub.

```bash
$ docker build --rm -t marketplace:latest .
```

jika sudah jadi docker image nya , kita push ke docker hub sebelum itu masuk ke [https://hub.docker.com/](https://hub.docker.com/) create repository. dan sebelum menggunakan command **tag and push** seharus nya login dahulu dengan perintah.

```bash
$ docker login
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674391639922/bc9f9186-b06d-401f-8c26-1189b9c74df6.webp align="center")