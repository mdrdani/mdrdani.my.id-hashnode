---
title: "Migrate a MySQL Database to Google Cloud SQL"
datePublished: Tue Aug 08 2023 03:38:34 GMT+0000 (Coordinated Universal Time)
cuid: cll1r4saa000009lagbokblmv
slug: migrate-a-mysql-database-to-google-cloud-sql
canonical: https://www.cloudskillsboost.google/games/4301/labs/27864
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/SYTO3xs06fU/upload/f825470f36dd44fb1cf4f159f030e2b2.jpeg
tags: compute-engine, wordpress, mysql, google-cloud-platform, cloud-sql

---

### **Challenge scenario**

Blog WordPress kita berjalan pada server yang tidak lagi sesuai. Sebagai bagian pertama dari latihan migrasi lengkap, kita sedang memigrasikan database yang di-host secara lokal yang digunakan oleh blog ke Cloud SQL.

Instalasi WordPress yang sudah ada terpasang di direktori /var/www/html/wordpress pada instans yang disebut blog yang sudah berjalan di lab. kita dapat mengakses blog dengan membuka peramban web dan mengarahkan ke alamat IP eksternal instans blog.

Database yang sudah ada untuk blog disediakan oleh MySQL yang berjalan pada server yang sama. Database MySQL yang sudah ada disebut **wordpress** dan pengguna disebut **blogadmin** dengan kata sandi **Password1\***, yang memberikan akses penuh ke database tersebut.

Buat instance SQL Database.

```bash
export ZONE=<SET ZONE> //us-central1-a
gcloud sql instances create wordpress --tier=db-n1-standard-1 --activation-policy=ALWAYS --gce-zone $ZONE
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691464226239/1eb7884b-0845-40cf-9f43-8be0c95564a4.png align="center")

set password SQL Database nya .

```bash
gcloud sql users set-password --host % root --instance wordpress --password Password1*
```

authorize IP EXTERNAL dari Instance **Blog** ke **Instance SQL wordpress.**

```bash
export ADDRESS=<IP EXTERNAL DARI COMPUTE BLOG> //104.196.5.158/32
gcloud sql instances patch wordpress --authorized-networks $ADDRESS --quiet
```

SSH ke instance **Blog** yang sudah disediakan lab, di dalam instance **Blog** remote ke Instance SQL **wordpress**

```bash
gcloud compute ssh blog
MYSQLIP=$(gcloud sql instances describe wordpress --format="value(ipAddresses.ipAddress)")
mysql --host=$MYSQLIP \
    --user=root --password
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691465097604/734a91be-e779-4734-84f3-3c12f57f2cb6.png align="center")

buat database dengan nama wordpress.

```bash
CREATE DATABASE wordpress;
CREATE USER 'blogadmin'@'%' IDENTIFIED BY 'Password1*';
GRANT ALL PRIVILEGES ON wordpress.* TO 'blogadmin'@'%';
FLUSH PRIVILEGES;
exit
```

selanjutnya buat dump database **wordpress** dan export ke Instance Database **wordpress**.

```bash
sudo mysqldump -u root -pPassword1* wordpress > wordpress_backup.sql

mysql --host=$MYSQLIP --user=root -pPassword1* --verbose wordpress < wordpress_backup.sql
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691465291046/deb8376d-bc3b-46d9-9156-b1d0f543eacf.png align="center")

selanjutnya restart service **apache,** dan configurasi **wp-config.php**

```bash
sudo service apache2 restart

cd /var/www/html/wordpress

sudo nano wp-config.php
```

replace IP HOST MYSQL dari **localhost** ke **EXTERNAL IP SQL.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691465621878/2f2076a9-1baa-4ba2-a0ef-1271eadfd094.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691465670462/86d627fd-07c8-40b8-94ea-80229bb73132.png align="center")