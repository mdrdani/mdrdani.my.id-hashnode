---
title: "Evolution of a website: Going from single server to serverless"
datePublished: Mon Aug 07 2023 14:57:54 GMT+0000 (Coordinated Universal Time)
cuid: cll0zykj2000209laek5v8rcq
slug: evolution-of-a-website-going-from-single-server-to-serverless
canonical: https://training.resources.awscloud.com/migration-traincert/3-free-builder-labs-for-beginners-and-pros-alike?trk=ddcf6425-f792-4df1-86b7-54b786f81162&sc_channel=el
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/67l-QujB14w/upload/94378402294800c270638788d0ca53e9.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1691420257647/20b6a143-4d00-4402-ba81-fa54f8fd4760.png
tags: ec2, aws, dynamodb, s3

---

### Lab Overview

Kita dipekerjakan oleh AnyCompany Ice Cream untuk membuat dan memelihara situs web mereka. Dalam proyek ini, Kita mengikuti perkembangan bisnis ini dan bagaimana infrastruktur situs web mereka berkembang.

Kita memulai dengan membuat situs web statis perusahaan yang dihosting di Layanan Penyimpanan Sederhana Amazon (Amazon S3) yang menampilkan berbagai rasa es krim. Bisnis ini mulai populer di kalangan penduduk setempat dan rasa-rasa tersebut mulai habis terjual sebelum hari berakhir. Pemilik bisnis meminta Kita untuk beralih ke sebuah server di mana mereka bisa mendapatkan pembaruan dari Kita. Mereka memutuskan untuk bermigrasi dari situs statis mereka ke Amazon Elastic Compute Cloud (EC2) dengan tumpukan Linux, Apache, MySQL, PHP (LAMP) yang dibangun di atasnya.

Hal ini akan memungkinkan situs web untuk diperbarui dan bereaksi secara dinamis. Permintaan untuk es krim AnyCompany melonjak setelah video viral, dan infrastruktur serta pemeliharaan situs menjadi sulit untuk dikelola. Untuk mengatasi peningkatan permintaan, Kita menyarankan untuk memindahkan bagian-bagian dari tumpukan LAMP mereka ke layanan serverless, dimulai dengan database MySQL. Database MySQL akan digantikan dengan database serverless Amazon DynamoDB.

---

Kurang lebih Lab Overview dari **3 FREE Builder Labs for Beginners and Pros alike** yang akan kita pelajari sekarang.

### Task 1 : Upload files to Amazon S3

**TASK 1.1: DOWNLOAD THE** [**STATIC-SITE.ZIP**](https://us-west-2-aws-training.s3.amazonaws.com/courses/SPL-TF-100-NWSSTS/v1.0.2.prod-0837bec4/scripts/static-site.zip) **FILE**

download the [**static-site.zip**](https://us-west-2-aws-training.s3.amazonaws.com/courses/SPL-TF-100-NWSSTS/v1.0.2.prod-0837bec4/scripts/static-site.zip) yang isinya static website files ke PC pribadi.

Unzip **static-site.zip.**

**TASK 1.2: UPLOAD THE FILES TO AMAZON S3**

pilih bucket nya dengan nama **s3hmtlbucket**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394348096/55755d62-5ae1-4393-a6f5-6453ff5a976f.png align="center")

drag file static-site yang sudah di extract tadi.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394419235/61f0e3cd-1158-4af4-8f82-a0c9c4a9c25e.png align="center")

pilih **upload** di bawah halaman, jika sudah success klik **close**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394586649/d3e6f800-66b0-4021-926f-a0be6c011bca.png align="center")

TASK 1.3: TEST THE STATIC S3 WEBSITE

Pada **Amazon S3 buckets** ke bagian **properties** &gt; **static website hosting**. Copy link url dan paste ke dalam browser.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394671747/4e262651-fe3b-4604-9a75-d437967b9fbd.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394683877/0a002377-ce96-4461-9d13-7cfd1add149a.png align="center")

done

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691394702376/b21f78cf-5863-42a1-b029-61d7eb96c4ee.png align="center")

### **Task 2: Verify the dynamic website on Amazon EC2 is running**

Bisnis mulai tumbuh, dan pelanggan ingin fitur di situs web di mana mereka bisa melihat rasa es terbaru atau ketika suatu rasa sudah habis terjual. Kita menyarankan untuk memindahkan server ke instansi Amazon EC2 dengan LAMP terpasang di dalamnya. Ini memungkinkan situs untuk menghasilkan konten dinamis menggunakan database dan memungkinkan Kita untuk memperbarui rasa saat stok ditambahkan atau habis terjual.

Dalam tugas ini, Kita memverifikasi bahwa situs web dinamis baru berfungsi dan bahwa data tentang inventaris rasa es krim yang tersedia diambil dari database. Kita menyalin alamat IP untuk instansi EC2 dan menempelkannya ke tab browser baru untuk melihat apakah situs web berhasil dimuat.

Untuk membantu mempercepat langkah ini, tugas ini sudah dikonfigurasi dengan Amazon VPC dan sumber daya jaringannya, serta Amazon EC2 dengan tumpukan LAMP yang sudah dikonfigurasi. Beberapa tindakan berikut sudah selesai dilakukan.

* [LAMP stack](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-lamp-amazon-linux-2.html) sudah di install sebagai web server untuk host AnyCompany’s dynamic website.
    
* Selain itu, sebuah Kebijakan IAM digunakan untuk mendefinisikan izin-izin untuk setiap layanan yang digunakan..
    
* Rasa dan harga sekarang diperbarui melalui database MySQL yang secara dinamis memperbarui situs web.
    

![Dynamic architecture](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/SPL-TF-100-NWSSTS/v1.0.2.prod-0837bec4/instructions/en_us/images/dynamic_site.png align="left")

di atas pada AWS Management Console, di bagian search bar, search for and choose **EC2**, dan pilih instance dengan nama **ice cream.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395300199/a9d51fbf-9ef4-4c93-bab5-97c46836fd77.png align="center")

copy public IPv4 Address ke dalam browser

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395326841/d41cd8e3-82d6-40bb-85fd-63602d250d31.png align="center")

### **Task 3: Update dynamic website to a serverless solution using DynamoDB for the MySQL database**

Bisnis terus tumbuh, tetapi dengan video baru-baru ini tentang pengalaman pelanggan yang kemudian menjadi viral, situs web AnyCompany Ice Cream telah mengalami lonjakan lalu lintas web yang melebihi kemampuan server database MySQL saat ini. Sayangnya, pelanggan mengalami kesalahan saat mengakses situs web. Untuk mengatasi ini, Kita ditugaskan untuk memindahkan database MySQL lokal ke Amazon DynamoDB karena dapat melibatkan permintaan lalu lintas web pelanggan untuk memastikan pelanggan terus dapat mengakses situs web AnyCompany Ice Cream tanpa gangguan.

![Dynamic architecture](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/SPL-TF-100-NWSSTS/v1.0.2.prod-0837bec4/instructions/en_us/images/serverless.png align="left")

TASK 3.1: UPDATE THE AMAZON DYNAMODB PRODUCTS TABLE WITH A NEW ICE CREAM FLAVOR

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395458034/7258e2c2-2f17-4cce-81f0-691a341d932c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395631921/c99c8449-565b-4949-992d-b2b02592ca89.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395728913/3b52f207-c9cd-4f10-86af-f861520b81de.png align="center")

TASK 3.2: UPDATE THE WEBSITE TO USE AMAZON DYNAMODB

ke service **EC2** pada search bar AWS. di dalamnya pilih nama instance **Ice Cream.** selanjutnya klik **connect** di sampai di bagin ini, untuk username ec2-user

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395836920/68706c9f-2d0e-4701-b0c4-df9f0b54f737.png align="center")

masuk ke AWS console, jalankan automation tersbut dengan perintah **/var/www/html/migrate-database.sh**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691395881497/3fdbabcc-ba5f-46fe-bfe7-67a3b8f68349.png align="center")

berikut ini isi dari file migrate-database.sh kemudian jalankan

```bash
# put this in /var/www/html

echo "Renaming index.php to compute-index.php" 
mv /var/www/html/index.php /var/www/html/compute-index.php

echo "Renaming serverless-index.php to index.php"
mv /var/www/html/serverless-index.php /var/www/html/index.php

echo "Restarting Apache"
sudo systemctl restart httpd
```

serverless-index.php

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691419681765/0dce46be-5ce0-4aad-9ede-599efdc7513e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691419716530/ccebd671-2f08-4fcb-9500-f03566f9348d.png align="center")

done.

![Website with new ice cream flavor listed](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/SPL-TF-100-NWSSTS/v1.0.2.prod-0837bec4/instructions/en_us/images/website-dynamodb-product-listing.png align="left")

AWS Traning and Certifiaction ([https://learn.labs.awsevents.com/](https://learn.labs.awsevents.com/))