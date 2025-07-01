---
title: "Linux Hands-on 1 (File Management and Directory Operations)"
datePublished: Wed Feb 05 2025 09:12:41 GMT+0000 (Coordinated Universal Time)
cuid: cm6roxf9y000909jufp8acsoe
slug: linux-hands-on-1-file-management-and-directory-operations
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/xbEVM6oJ1Fs/upload/8f2ed088392f9234c8404c126c04c420.jpeg
tags: ubuntu, linux

---

Artikel ini akan menceritakan pengalaman saya menyelesaikan tugas [*Community DevOps Focus*](https://discord.gg/8bUq4aNT) *Group*. Tugas ini terdiri dari 4 bagian, dan saya akan memisahkannya ke dalam beberapa artikel. Pada artikel pertama ini, saya akan berfokus pada tugas-tugas yang berkaitan dengan file management dan directory operations.

Sebelum menjalankan perintah pertama, kita perlu memasang `flog` untuk menghasilkan *log* palsu dan `stress` untuk mensimulasikan beban CPU dan memori. Saya juga memasang `tree` untuk mempermudah visualisasi struktur direktori.

```plaintext
https://github.com/mingrammer/flog
```

```bash
sudo apt install stress
sudo apt install tree
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738744387921/42a21dd2-a4fa-4c0f-a1f3-938da81a584f.png align="center")

## Task 1.1: Create the following directory structure:

```bash
mkdir -p project/reports/2024 project/reports/2025 project/scripts project/data
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738744540754/abb51287-24d2-4d16-b397-e0b72f67b1e3.png align="center")

## Task 1.2: Create empty files in reports/2024

```bash
touch project/reports/2024/q1.txt
touch project/reports/2024/q2.txt
touch project/reports/2024/q3.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738744803618/07d26f06-5dba-4e5f-9362-e8661a6f04bc.png align="center")

Kita bisa pakai perintah `touch` buat bikin file. Perintah `ls` digunakan untuk menampilkan daftar berkas dan direktori. Berikut penjelasan opsi yang digunakan:

* `-l`: Menampilkan daftar dalam format *long listing* (panjang), yang memberikan informasi detail seperti izin berkas, pemilik, grup, ukuran, tanggal modifikasi, dan nama berkas.
    
* `-h`: Menampilkan ukuran berkas dalam format *human-readable* (mudah dibaca manusia), misalnya KB, MB, atau GB, bukan dalam *byte*.
    
* `-t`: Mengurutkan daftar berdasarkan waktu modifikasi terakhir.
    

Jadi, `ls -lht` akan menampilkan daftar berkas dan direktori dalam format panjang, dengan ukuran yang mudah dibaca, dan diurutkan berdasarkan waktu modifikasi terakhir.

**Penjelasan *File Permission* (Izin Berkas) di Linux:**

Izin berkas di Linux mengatur siapa yang boleh melakukan apa terhadap suatu berkas. Izin ini direpresentasikan dalam sepuluh karakter, contohnya `-rw-rw-r--`.

* Karakter pertama (`-`): Menunjukkan jenis berkas. `-` berarti berkas biasa, `d` berarti direktori, `l` berarti *symbolic link*, dan lainnya.
    
* Sembilan karakter berikutnya dibagi menjadi tiga kelompok, masing-masing untuk *owner* (pemilik), *group* (kelompok), dan *others* (lainnya).
    
* Setiap kelompok memiliki tiga bit:
    
    * `r`: *Read* (baca) - Izin untuk membaca isi berkas.
        
    * `w`: *Write* (tulis) - Izin untuk mengubah atau menghapus isi berkas.
        
    * `x`: *Execute* (eksekusi) - Izin untuk menjalankan berkas (jika berkas tersebut adalah program yang dapat dieksekusi).
        
* Tanda `-` berarti izin tersebut tidak diberikan.
    

Dalam contoh `-rw-rw-r--`:

* *Owner*: Memiliki izin baca (`r`) dan tulis (`w`), tetapi tidak memiliki izin eksekusi (`-`).
    
* *Group*: Memiliki izin baca (`r`) dan tulis (`w`), tetapi tidak memiliki izin eksekusi (`-`).
    
* *Others*: Memiliki izin baca (`r`), tetapi tidak memiliki izin tulis (`-`) dan eksekusi (`-`).
    

## Task 1.3: Move q1.txt to 2025

```bash
mv project/reports/2024/q1.txt project/reports/2025/
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738745074985/4cd1ff47-5397-4acc-adb7-49d7bbd5b700.png align="center")

`mv` itu artinya "move" alias mindahin. Nah, biar nggak nyasar, mending tulis perintah lengkap sama alamat lengkap filenya, jangan cuma `q1.txt` doang. Kalau tiba-tiba muncul pesan "No such file or directory", itu biasanya gara-gara alamat filenya salah.

## Task 1.4: Copy q2.txt to scripts dan rename menjadi analysis.txt

```bash
cp project/reports/2024/q1.txt project/scripts/analysis.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738745416076/da5c1ad1-06e2-4f7e-98a4-4a41b9eb811f.png align="center")

## Task 1.5: list files in 2025 and save output to file file\_list.log

```bash
ls project/reports/2025 > file_list.log
```

## Task 1.6: Append “Task Completed” to q3.txt

```bash
echo "Task Completed" >> project/reports/2024/q3.txt
cat project/reports/2024/q3.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738745656456/46e18c6e-82c7-4598-89bf-c7732a25ff2d.png align="center")

* `echo "Task Completed"`: Nah, bagian ini artinya kita mau "ngomong" atau "nulis" kalimat "Task Completed". `echo` itu kayak perintah buat nampilin sesuatu di layar, tapi di sini kita mau tampilinnya di dalam file.
    
* `>>`: Tanda ini nih yang penting. Ini artinya kita mau "nambahin" tulisan "Task Completed" tadi ke dalam file. Jadi, kalau di file udah ada tulisan lain, tulisan "Task Completed" ini bakal ditaruh di baris baru, di bawah tulisan yang lama. Beda kalau kita pakai tanda `>`, kalau pakai ini, tulisan yang lama di file akan dihapus dan diganti dengan tulisan "Task Completed".
    
* `cat`: Nah, kalau `cat` ini kayak baca buku. Dia bakal nampilin isi file ke layar.
    

## Task 1.7: Display directory structure and save to structure.log

```bash
tree project/ > structure.log
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738745885088/1a403379-0c80-44be-9ee0-5796e28495e2.png align="center")

`tree`: Nah, perintah `tree` ini kayak tukang kebun yang rapi banget. Dia bakal nunjukin struktur folder dan file kita dalam bentuk pohon, lengkap dengan cabang-cabangnya. Jadi, kita bisa lihat dengan jelas, file ini ada di dalam folder apa, folder itu ada di dalam folder apa lagi, dan seterusnya.

## Task 1.8: Delete analysis.txt and confirm

```bash
 rm project/scripts/analysis.txt
```

`rm`: Nah, kalau `rm` ini artinya "remove" alias hapus. Jadi, perintah ini digunakan untuk menghapus file atau folder.

## Task 1.9: Copy the entire project directory

```bash
 cp -r project/ project-old
```

* `cp`: Nah, kalau `cp` ini artinya "copy" alias salin. Jadi, perintah ini digunakan untuk menyalin file atau folder.
    
* `-r`: Opsi `-r` ini singkatan dari "recursive". Artinya, kalau yang disalin itu folder, maka semua isinya (termasuk subfolder dan file-file di dalamnya) juga ikut disalin.
    
* `project/`: Ini adalah lokasi folder yang mau kita salin. Dalam contoh ini, kita mau menyalin folder `project/` beserta seluruh isinya.
    
* `project-old`: Ini adalah nama folder baru hasil salinan. Jadi, kita akan membuat folder baru bernama `project-old` yang isinya sama persis dengan folder `project/`.
    

## Task 1.10: Delete Original project

```bash
rm -ri project
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738746475666/80f418d5-ad39-4797-a92f-1acea7fd0a33.png align="center")

* `rm`: Nah, kalau `rm` ini artinya "remove" alias hapus. Jadi, perintah ini digunakan untuk menghapus file atau folder.
    
* `-r`: Opsi `-r` ini singkatan dari "recursive". Artinya, kalau yang dihapus itu folder, maka semua isinya (termasuk subfolder dan file-file di dalamnya) juga ikut dihapus.
    
* `-i`: Opsi `-i` ini singkatan dari "interactive". Artinya, sebelum menghapus setiap file atau folder, perintah ini akan bertanya dulu ke kita, "Yakin mau dihapus nggak nih?". Jadi, kita punya kesempatan untuk berpikir ulang sebelum file atau folder benar-benar hilang.
    
* `project`: Ini adalah nama folder yang mau kita hapus beserta seluruh isinya.
    

## Task 1.11: Create a 5GB file

```bash
truncate -s 5G large_file.dat
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738746631800/436c3f97-2318-4580-a976-35b81e3f61a0.png align="center")

* `truncate`: Nah, kalau `truncate` ini artinya "memotong" atau "memangkas". Perintah ini digunakan untuk mengubah ukuran file, bisa memperbesar atau memperkecil.
    
* `-s 5G`: Opsi `-s` ini digunakan untuk menentukan ukuran file yang baru. Dalam contoh ini, kita mau membuat ukuran file menjadi 5 Gigabyte (5G). Kamu bisa menggunakan satuan lain seperti K (Kilobyte), M (Megabyte), atau G (Gigabyte).
    
* `large_file.dat`: Ini adalah nama file yang ukurannya mau kita ubah.