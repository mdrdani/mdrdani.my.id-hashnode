---
title: "Encrypt/Decrypt RSA Key With FiloSottile/age & Mozilla/sops"
seoTitle: "Encrypt/Decrypt RSA Key With FiloSottile/age & Mozilla/sops"
seoDescription: "Encrypt/Decrypt RSA Key With FiloSottile/age & Mozilla/sops"
datePublished: Tue Dec 20 2022 14:33:37 GMT+0000 (Coordinated Universal Time)
cuid: cld7hcsip000209mmhy0vdxct
slug: encryptdecrypt-rsa-key-with-filosottileage-mozillasops
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1674397716383/7d8ab1ad-0a39-41c6-b3c3-94e34515ec9b.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1674398003713/9fdcec38-f036-42b7-ba2e-efb7cfc45637.png
tags: encryption, filosottileage, mozillasops

---

Bahas soal perihal keamanan nih guys, ini perihal bagaimana kita mengenkripsi RSA key jika kita berencana untuk publish ke publik internet seperti Github atau yang lain-lain, tapi tidak di rekomendasi kan untuk di publish publik internet. Di sini kita menggunakan **Mozilla/sops &gt;&gt; SOPS: Secrets OPerationS.**

Sebelum itu kita install dahulu **Mozilla/sops** nya di perangkat kita bisa di download di [https://github.com/mozilla/sops/releases](https://github.com/mozilla/sops/releases)**.** Ada untuk Windows, Mac, dan Linux. Di sini saya memakai **sops** version 3.7.0.

Sebagai sample saya akan menggenerate SSH Key menggunakan Windows Powershell 7.3.1

```bash
$ ssh-keygen -C ubuntu
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397741230/0df1602d-20ef-4f31-816b-0d7a532a2e8a.jpeg align="center")

Pertama kita akan generate dahulu menggunakan **FiloSottile/age (**[**https://github.com/FiloSottile/age**](https://github.com/FiloSottile/age)**) &gt;&gt;** About A simple, modern and secure encryption tool (and Go library) with small explicit keys, no config options, and UNIX-style composability. jika belum di install coba di install dahulu, keguaan ini sama saja dengan private key dan public key. Di sini saya menggunakan **age** version 1.0.0.

```bash
$ age-keygen -o agekey
```

**nama agekey** bisa dirubah sesuai keinginan. Jika sudah akan tergenerate kurang lebih seperti ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397767752/51f94d24-a286-4b5c-8484-26ec56cb4fdd.jpeg align="center")

nah selanjut nya cara encrypt key nya, copy dahulu public key yang ada di **agekey** selanjutnya ketik comand.

```bash
$ sops --encrypt --age *publickeynya* ubuntu-rsa >> ubuntu-rsa.encrypted
```

Hasilnya seperti ini

![Before Encrypt](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397788061/50df207c-8e84-4887-bb13-8802b542dcde.jpeg align="center")

![After Encrypt](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397833336/be5f7ddc-e72f-4e1f-835c-5fb4a763c56f.jpeg align="center")

untuk decrypt lakukan command

```bash
$ sops --decrypt -i  ubuntu-rsa.encrypted
```

pasti ada error kan :D buru2 amat mau decrypt.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397891583/ae41774f-819d-4ede-94fb-5c1c87284b2c.jpeg align="center")

di situ sudah terlihat jadi di file keys.txt memerlukan private key di masukan ke file tsb untuk mendecrypt nya.

Buka file **..\\AppData\\Roaming\\sops\\age\\keys.txt.** Jika tidak ada folder sops ...... tinggal di buat saja.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397912254/a22e05a4-5ab6-4413-865a-c205b77ea3c7.jpeg align="center")

jalankan kembali command

```bash
$ sops --decrypt -i  ubuntu-rsa.encrypted
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397939283/c4e600f5-29c9-4615-a1d0-7d5ab63b5c49.jpeg align="center")

oke cakeep file sudah di decrypt.