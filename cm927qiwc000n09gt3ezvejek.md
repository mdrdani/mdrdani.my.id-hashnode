---
title: "Challenge 2 : Configure Resource Limits Linux"
datePublished: Fri Apr 04 2025 03:16:18 GMT+0000 (Coordinated Universal Time)
cuid: cm927qiwc000n09gt3ezvejek
slug: challenge-2-configure-resource-limits-linux
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/yFbyvpEGHFQ/upload/28a42130fa32a048203233e138f414f8.jpeg
tags: linux

---

Pernahkah Anda bertanya-tanya bagaimana cara mencegah satu grup atau pengguna menghabiskan seluruh sumber daya server Linux Anda? Nah, di postingan kali ini, kita akan memecahkan masalah yang di berikan [adinusa.id](https://adinusa.id) dengan mengkonfigurasi *resource limits.* Mengelola sumber daya sistem secara efisien adalah kunci untuk menjaga performa server Linux yang optimal.

## Intructions

1. Configure resource limits for local group **student**, so that a maximum of **100 processes** can be start.
    
2. Configure resource limits for local user **student**, so that a maximum open file is **3000**, and configure current limit is **2000.**
    
3. The above process limit should run when the student user logs in for the first time, ex: after rebooting
    

---

### Configure resource limits for local group **student**, so that a maximum of **100 processes** can be start.

Untuk konfigurasi resource limit group student langkah pertama kita check terlebih dahulu apakah group student sudah ada menggunakan command linux

```bash
getent group student
```

Jika sudah ada group **student,** maka selanjutnya edit file **/etc/security/limits.conf** dan tambahkan baris ini di dalamnya

```bash
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#ftp             -       chroot          /ftp
#@student        -       maxlogins       4

@student        hard    nproc           100
```

Keterangan:

* `@student` → Menunjukkan bahwa ini berlaku untuk grup `student`.
    
* `hard nproc 100` → Batas maksimal proses yang bisa dijalankan
    

---

### Configure resource limits for local user **student**, so that a maximum open file is **3000**, and configure current limit is **2000.**

jika tadi kita konfigurasi untuk group nya maka selanjutnya kita konfigurasi untuk user nya. Langkah Pertama kita check apakah ada user **student** dan adakah user di dalam group **student**.**.**

```bash
id student
```

```bash
uid=1000(student) gid=1000(student) groups=1000(student),100(users),117(admin)
```

jika tidak ada di dalam group student, kita bisa gunakan

```bash
sudo usermod -aG student student
```

selanjutnya untuk melimit akun student ini kita edit file **/etc/security/limits.conf** kembali dengan menambahkan baris berikut

```bash
#ftp             hard    nproc           0
#ftp             -       chroot          /ftp
#@student        -       maxlogins       4

@student        hard    nproc           100

student         soft    nofile          2000
student         hard    nofile          3000
```

Keterangan :

* `soft nofile 2000` → Batas awal (soft limit) untuk file terbuka.
    
* `hard nofile 3000` → Batas maksimal (hard limit) untuk file terbuka.
    

---

### The above process limit should run when the student user logs in for the first time, ex: after rebooting

untuk memproses limit ketika user login pertama kali maka kita kita perlu mengedit file **/etc/pam.d/common-session**

```bash
session required pam_limits.so
```

```bash
# See "man pam_umask".
session optional                        pam_umask.so
# and here are more per-package modules (the "Additional" block)
session required        pam_unix.so
session optional        pam_systemd.so
session required        pam_limits.so
# end of pam-auth-update config
```

dan edit juga file **/etc/pam.d/common-session-noninteractive**

```bash
# See "man pam_umask".
session optional                        pam_umask.so
# and here are more per-package modules (the "Additional" block)
session required        pam_unix.so
session required        pam_limits.so
# end of pam-auth-update config
```

Ini memastikan bahwa batasan dari `/etc/security/limits.conf` diterapkan saat login.

---

Jika ingin mengatur **nice value** untuk pengguna student, dapat menambahkan baris

```bash
student hard priority 10
student soft priority 10
```

#### **Apa Itu Nice Value?**

`nice` value adalah tingkat prioritas suatu proses di sistem Linux. Nilai ini menentukan seberapa banyak CPU time yang diberikan ke proses tertentu dibandingkan dengan proses lain.

* Nilai **rendah (misal -20)** berarti proses memiliki **prioritas tinggi** (lebih diutamakan oleh CPU).
    
* Nilai **tinggi (misal 19)** berarti proses memiliki **prioritas rendah** (kurang diutamakan oleh CPU).
    

Secara default, **nice value** untuk proses yang berjalan adalah **0**.

```bash
#@student        -       maxlogins       4

@student        hard    nproc           100

student         soft    nofile          2000
student         hard    nofile          3000

student         hard    priority        10
student         soft    priority        10

# End of file
```

#### **Mengapa Mengatur Nice Value?**

Mengatur **nice value** untuk pengguna bertujuan untuk:

* **Mengontrol alokasi CPU** untuk proses yang dibuat oleh user.
    
* **Mencegah pengguna** mengambil terlalu banyak CPU, sehingga proses penting lain tetap berjalan optimal.
    
* **Membantu manajemen sumber daya dalam sistem multi-user**.
    

**Cek Nice Value Default**  
Jalankan perintah berikut sebagai `student`:

```bash
nice
```

Jika konfigurasi benar, outputnya harus:

```bash
10
```