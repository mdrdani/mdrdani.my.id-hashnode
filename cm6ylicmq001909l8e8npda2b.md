---
title: "Linux Hands-on 3 (Process Management)"
datePublished: Mon Feb 10 2025 05:11:22 GMT+0000 (Coordinated Universal Time)
cuid: cm6ylicmq001909l8e8npda2b
slug: linux-hands-on-3-process-management
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/wh-RPfR_3_M/upload/d96792d996b03a77e9334600ed0fdc8a.jpeg
tags: linux, linux-for-beginners

---

Sebagai pengguna Linux, Anda mungkin sudah familiar dengan perintah-perintah seperti `ps`, `top`, atau `kill`. Namun, tahukah Anda bagaimana cara kerja perintah-perintah tersebut dan bagaimana memanfaatkannya secara maksimal? Mari kita telusuri lebih dalam tentang *Linux Process Management*

## Task 3.1: Start a Long-Running Process

```bash
sleep 500 &
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739149764046/62ca8dee-8911-41f0-ae49-917daf006f9f.png align="center")

Jadi, gini, di dunia Linux, kadang kita perlu "menunda" sesuatu. Nah, perintah `sleep` ini gunanya buat itu, semacam ngasih "jeda" sementara. Angka 500 di sini artinya kita mau nunda selama 500 detik.

Terus, tanda `&` di belakangnya itu apa? Nah, ini yang bikin seru. Tanda ini artinya kita mau perintah `sleep` ini jalan di "latar belakang" atau *background*. Jadi, sementara dia lagi "tidur" selama 500 detik, kita masih bisa tetep ngapa-ngapain di terminal, nggak perlu nungguin dia selesai.

## Task 3.2: Simulate CPU and memory usage

```bash
stress --cpu 1 --vm 1 --vm-bytes 512M --timeout 30s
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739154729632/fa91c25d-6aa2-44fa-98c5-26213f02da26.png align="center")

Komando ini adalah cara buat "menyiksa" komputer Linux kita, alias memberikan tekanan atau *stress test* biar kita tahu seberapa kuat dia. `stress` adalah alat yang populer untuk melakukan pengujian semacam ini.

* `stress`: Ini adalah nama perintahnya, yaitu program `stress` itu sendiri.
    
* `--cpu 1`: Artinya, kita mau membebani CPU dengan 1 prosesor (atau core). Kalau komputer kamu punya banyak core, kamu bisa ganti angka 1 jadi angka lain sesuai kebutuhan.
    
* `--vm 1`: Artinya, kita mau membebani memori virtual (swap) dengan 1 proses.
    
* `--vm-bytes 512M`: Nah, ini yang menarik. Kita mau alokasikan 512 Megabyte (M) memori virtual untuk pengujian ini. Jadi, `stress` akan "pura-pura" menggunakan memori sebanyak ini.
    
* `--timeout 30s`: Artinya, kita mau batasi waktu pengujian hanya selama 30 detik. Jadi, setelah 30 detik, `stress` akan berhenti otomatis.
    

**Kenapa pakai perintah ini?**

* **Uji Ketahanan:** Buat lihat seberapa kuat komputer kita, apakah dia stabil kalau dipaksa kerja keras.
    
* **Cari Masalah:** Kadang, kalau ada masalah hardware atau software, baru ketahuan pas komputer lagi "stress".
    
* **Benchmark:** Buat membandingkan performa komputer yang berbeda.
    

## Task 3.3: Monitor System Usage

```bash
top
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739154839321/eb3acb9e-d0b7-4ef7-bc26-af3266f9a6a5.png align="center")

```bash
sudo apt install htop
htop
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739154886608/b5015e9f-f558-444d-a96c-7d424cb97d58.png align="center")

Singkatnya, `top` dan `htop` adalah alat yang sangat berguna di Linux untuk memantau aktivitas sistem secara *real-time*. Keduanya menampilkan daftar proses yang sedang berjalan, penggunaan CPU, memori, dan informasi penting lainnya. Anggap saja mereka seperti "jendela" yang menunjukkan apa yang sedang terjadi di dalam komputer Linux kita.

Keduanya menampilkan informasi yang mirip, antara lain:

* **Daftar Proses**: Menampilkan semua proses yang sedang berjalan di sistem, lengkap dengan ID proses (PID), penggunaan CPU, memori, dan lain-lain.
    
* **Penggunaan CPU**: Menunjukkan berapa persen CPU yang sedang digunakan.
    
* **Penggunaan Memori**: Menunjukkan berapa banyak memori (RAM) yang sedang digunakan.
    
* **Waktu Sistem**: Menunjukkan sudah berapa lama sistem berjalan.
    

## Task 3.4: Verify the sleep process

```bash
ps -aux | grep sleep
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739154991237/73c7e842-bba5-473b-8dfd-2f5a33edd7d9.png align="center")

* `ps -aux`: Perintah `ps` digunakan untuk menampilkan informasi tentang proses yang sedang berjalan. Opsi `-aux` memberikan informasi yang lebih lengkap dan detail, termasuk semua proses (baik yang dimiliki oleh pengguna maupun sistem), dalam format yang mudah dibaca.
    
* `grep sleep`: Perintah `grep` digunakan untuk mencari pola teks tertentu dalam output dari perintah lain. Dalam hal ini, kita mencari kata "sleep".
    
* Tanda `|` (pipa) di antara kedua perintah tersebut berfungsi untuk "menyalurkan" output dari perintah `ps -aux` ke perintah `grep sleep`. Jadi, `grep sleep` akan mencari baris yang mengandung kata "sleep" dalam output dari `ps -aux`.
    

**Kenapa pakai perintah ini?**

Perintah ini sangat berguna untuk mencari proses yang berkaitan dengan perintah `sleep`. Misalnya, kamu ingin melihat apakah ada proses `sleep` yang sedang berjalan, atau kamu ingin mencari tahu berapa lama proses `sleep` tersebut sudah berjalan.

## Task 3.5: Check disk space usage

```bash
df -h
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739155089294/863413af-d51c-48c8-a69b-5534a0265095.png align="center")

```bash
du -h /home/sdb/ | sort -hr | head -n 10
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739155224053/2406c055-d482-49ea-9424-419f7cfde1eb.png align="center")

* `df -h`: Perintah `df` digunakan untuk menampilkan informasi tentang penggunaan ruang disk pada sistem. Opsi `-h` membuat ukuran ditampilkan dalam format yang mudah dibaca (misalnya, KB, MB, GB). Namun, perintah ini tidak akan membantu kita melihat ukuran per folder.
    
* `du -h /home/sdb/`: Perintah `du` digunakan untuk menampilkan penggunaan ruang disk per file dan direktori. Opsi `-h` sama seperti di `df`, membuat ukuran ditampilkan dalam format yang mudah dibaca. `/home/sdb/` adalah jalur folder yang ingin kita analisis.
    
* `|`: Tanda pipa ini berfungsi untuk "menyalurkan" output dari perintah sebelumnya ke perintah selanjutnya.
    
* `sort -hr`: Perintah `sort` digunakan untuk mengurutkan baris teks. Opsi `-h` mengurutkan berdasarkan ukuran yang mudah dibaca (misalnya, 10M lebih besar dari 2K), dan `-r` membalik urutan (dari terbesar ke terkecil).
    
* `head -n 10`: Perintah `head` digunakan untuk menampilkan beberapa baris pertama dari input. Opsi `-n 10` berarti kita hanya ingin menampilkan 10 baris pertama.
    

**Kenapa pakai perintah ini?**

Perintah ini sangat berguna untuk mencari folder atau file mana yang memakan ruang disk paling banyak. Dengan begitu, kita bisa tahu folder atau file mana yang bisa dihapus atau dipindahkan untuk mengosongkan ruang disk.

## Task 3.6: Monitor Memory Usage

```bash
free -h
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739155418060/b232709c-e69f-42cf-898a-ce1e8c45bf57.png align="center")

Komando `free -h` adalah perintah sederhana di Linux yang digunakan untuk menampilkan informasi tentang penggunaan memori sistem, baik memori fisik (RAM) maupun memori swap. Opsi `-h` membuat outputnya lebih mudah dibaca oleh manusia.

## Task 3.7: Log High CPU Usage Processes

```bash
while true; do ps -aux | awk '$3 > 10 {print $0}' >> high_cpu.log; sleep 5; done
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739155765888/50ba06c2-68f2-4de8-9fa1-f6b50ccb2994.png align="center")

* `while true`: Ini adalah awal dari *loop* tak terbatas. Artinya, perintah-perintah di dalam *loop* akan terus diulang sampai kita menghentikannya secara manual (biasanya dengan `Ctrl+C`).
    
* `do`: Ini menandai awal dari blok perintah yang akan diulang.
    
* `ps -aux`: Seperti yang sudah dibahas sebelumnya, perintah ini menampilkan informasi lengkap tentang semua proses yang sedang berjalan.
    
* `|`: Tanda pipa ini "menyalurkan" output dari `ps -aux` ke perintah selanjutnya.
    
* `awk '$3 > 10 {print $0}'`: Perintah `awk` digunakan untuk memfilter dan memproses teks. Dalam hal ini, kita memfilter baris-baris dari output `ps -aux` yang kolom ketiganya (`$3`, yang biasanya menunjukkan persentase penggunaan CPU) lebih besar dari 10. `{print $0}` mencetak seluruh baris jika kondisinya terpenuhi.
    
* `>> high_cpu.log`: Tanda `>>` digunakan untuk *append* (menambahkan) output dari perintah `awk` ke file `high_cpu.log`. Jadi, setiap kali ada proses yang CPU-nya di atas 10%, informasinya akan ditambahkan ke file ini.
    
* `sleep 5`: Perintah ini akan menunda eksekusi selama 5 detik. Jadi, *loop* akan berjalan setiap 5 detik.
    
* `done`: Ini menandai akhir dari blok perintah yang akan diulang.
    

**Kenapa pakai perintah ini?**

Perintah ini berguna untuk memantau proses-proses yang "rakus" CPU. Kita bisa melihat proses mana saja yang menggunakan CPU di atas ambang batas tertentu (dalam hal ini 10%) dan menganalisisnya.

## Task 3.8: Kill the long-running process

```bash
kill <PID>
```

Komando `kill` di Linux digunakan untuk mengakhiri atau "membunuh" sebuah proses yang sedang berjalan. `<PID>` adalah *Process ID*, yaitu nomor unik yang diberikan oleh sistem ke setiap proses yang sedang berjalan.

* `kill`: Ini adalah nama perintahnya, yaitu program `kill` itu sendiri.
    
* `<PID>`: Ini adalah *Process ID* dari proses yang ingin kita akhiri. Kita perlu mengganti `<PID>` dengan nomor PID yang sebenarnya.
    

**Cara Mendapatkan PID**

Sebelum menggunakan perintah `kill`, kita perlu tahu dulu PID dari proses yang ingin kita akhiri. Ada beberapa cara untuk mendapatkan PID, antara lain:

* Menggunakan perintah `top` atau `htop`: Kedua perintah ini menampilkan daftar proses yang sedang berjalan, lengkap dengan PID-nya.
    
* Menggunakan perintah `ps aux`: Perintah ini juga menampilkan daftar proses, dan kita bisa mencari PID dari proses yang ingin kita akhiri.