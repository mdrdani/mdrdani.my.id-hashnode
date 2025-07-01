---
title: "Linux Hands-on 4 (Networking Tools)"
seoTitle: "Linux Networking Tools Tutorial"
seoDescription: "Learn Linux networking tools: ping, telnet, curl, netstat, iperf, netcat, for network management through hands-on tasks"
datePublished: Mon Feb 10 2025 08:59:29 GMT+0000 (Coordinated Universal Time)
cuid: cm6ytnpew001409jj5tbx4fsp
slug: linux-hands-on-4-networking-tools
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/h3vT1Kp0FxA/upload/987d865b729a692f792e54b88655bea6.jpeg
tags: linux, linux-for-beginners

---

Di dunia Linux yang serba bisa ini, jaringan adalah urat nadi yang menghubungkan semuanya. Baik kamu seorang *sysadmin*, *developer*, atau sekadar pengguna yang penasaran, pemahaman tentang *networking tools* adalah kunci untuk menjelajahi dan mengendalikan jaringanmu.

## Task 4.1: Use ping to check connectivity to google.com

```bash
ping google.com > ping_output.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739174040307/62feed14-9fc0-41ed-9dab-c3513824dd83.png align="center")

Komando ini adalah kombinasi dari dua perintah Linux yang digunakan untuk menguji koneksi jaringan ke [`google.com`](http://google.com) dan menyimpan hasilnya ke dalam file bernama `ping_output.txt`.

* `ping` [`google.com`](http://google.com): Perintah `ping` digunakan untuk menguji konektivitas jaringan dengan mengirimkan paket data ke alamat IP tertentu (dalam hal ini, alamat IP dari [`google.com`](http://google.com)) dan menunggu balasan. Tujuannya adalah untuk mengetahui apakah komputer kita dapat terhubung ke alamat IP tersebut dan berapa lama waktu yang dibutuhkan untuk menerima balasan.
    
* `>`: Tanda ini adalah *redirection operator* yang digunakan untuk mengalihkan output dari suatu perintah ke file. Dalam hal ini, output dari perintah `ping` [`google.com`](http://google.com) akan dialihkan ke file `ping_output.txt`
    

**Kenapa pakai perintah ini?**

Perintah ini sangat berguna untuk:

* **Memeriksa Konektivitas**: Kita bisa tahu apakah komputer kita terhubung ke internet atau tidak.
    
* **Menguji Kecepatan Jaringan**: Kita bisa melihat seberapa cepat koneksi internet kita berdasarkan waktu tempuh bolak-balik.
    
* **Mencari Masalah Jaringan**: Kalau ada masalah koneksi, kita bisa lihat dari output `ping`, misalnya apakah ada paket data yang hilang atau waktu tempuh bolak-balik yang terlalu lama.
    

## Task 4.2: Use ping with 10 packets

```bash
ping -c 10 google.com > ping_10_packets.txt
```

`-c 10`: Opsi ini adalah yang bikin beda. `-c` adalah singkatan dari "count". Angka 10 di belakangnya berarti kita hanya ingin mengirimkan 10 paket ping saja. Jadi, ping tidak akan berjalan terus-menerus, tapi hanya 10 kali saja.

## Task 4.3: Use ping with a packet size of 1000 Bytes

```bash
ping -s 1000 google.com > ping_large_packet.txt
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739174409187/77922a6d-d882-4f4c-a680-fa152768c8e9.png align="center")

`-s 1000`: Opsi ini adalah yang bikin beda. `-s` adalah singkatan dari "size". Angka 1000 di belakangnya berarti kita ingin mengirim paket ping dengan ukuran 1000 byte. Ukuran default paket ping biasanya lebih kecil dari ini.

**Kenapa pakai perintah ini?**

* **Tes Ukuran Paket:** Berguna untuk menguji kemampuan jaringan dalam menangani paket data berukuran besar.
    
* **Masalah MTU:** Bisa membantu mendeteksi masalah terkait MTU pada jaringan. Jika paket terlalu besar, router mungkin perlu memecahnya, yang bisa mempengaruhi performa.
    

## Task 4.4: Use telnet to test port 80 on discord.com

```bash
sudo apt install telnet
telnet discord.com 80
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739174777931/57df0189-27d0-416f-9ea3-b20e38703b28.png align="center")

`telnet`: Ini adalah nama perintahnya, yaitu program `telnet` itu sendiri. `telnet` adalah *tool* kuno, tapi masih berguna untuk beberapa tujuan.

Perlu diingat bahwa `telnet` adalah *tool* yang sangat sederhana. Ia hanya membuat koneksi TCP, tanpa memahami protokol yang lebih tinggi seperti HTTP.

## Task 4.5: Use telnet to connect to the SMTP server on port 25

```bash
telnet smtp.gmail.com 25
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739174935241/06090b0d-9659-463a-b0ba-9551e8845081.png align="center")

Komando ini mencoba membuat koneksi ke server SMTP (Simple Mail Transfer Protocol) Gmail di [`smtp.gmail.com`](http://smtp.gmail.com) melalui *port* 25 menggunakan *tool* `telnet`.

## Task 4.6: Use curl to fetch content of https://discord.com

```bash
curl https://discord.com -o discord.html
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739175045138/fdb45a2d-6190-4dbe-b472-10d2bf394795.png align="center")

`curl`: Ini adalah nama perintahnya, yaitu program `curl` itu sendiri. `curl` adalah *tool* serbaguna yang digunakan untuk mentransfer data dari atau ke *server* menggunakan berbagai protokol, termasuk HTTP

Perintah ini sangat berguna untuk:

* **Mengunduh Kode Sumber**: Kita bisa melihat bagaimana sebuah halaman web dibuat dengan mengunduh kode sumbernya.
    
* ***Web Scraping***: Kita bisa mengambil data tertentu dari sebuah halaman web secara otomatis.
    
* ***Debugging***: Kita bisa melihat *error* atau masalah yang mungkin terjadi saat *load* halaman web.
    

## Task 4.7: Use curl with a custom User-Agent

```bash
curl -H "User-Agent: MyBrowser" https://google.com
#or
curl -L -H "User-Agent: MyBrowser" https://google.com
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739175283342/694600a3-77e4-4e1a-aa10-6e4def4aa535.png align="center")

`-H "User-Agent: MyBrowser"`: Opsi `-H` digunakan untuk menambahkan atau memodifikasi *header* HTTP yang dikirim. Dalam hal ini, kita mengubah *header* `User-Agent` menjadi "MyBrowser". *User-Agent* adalah *string* yang diidentifikasi *browser* atau aplikasi yang digunakan untuk mengakses *website*. Dengan mengubahnya, kita "berpura-pura" menjadi *browser* lain

`-L`: Opsi ini adalah yang membedakan kedua perintah ini. `-L` adalah singkatan dari "location". Opsi ini akan membuat `curl` mengikuti *redirect* HTTP jika *server* mengembalikannya

## Task 4.8: Use netstat to list active TCP connections

```bash
sudo apt install net-tools
netstat -at
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739175519575/8619982e-dcbd-4680-beab-12dbd2a517de.png align="center")

* `netstat`: Ini adalah nama perintahnya, yaitu program `netstat` itu sendiri. `netstat` adalah *tool* klasik untuk melihat statistik jaringan.
    
* `-a`: Opsi ini berarti "all", yang akan menampilkan semua koneksi yang sedang aktif, baik yang berstatus *listening* (menunggu koneksi masuk) maupun *established* (sudah terhubung).
    
* `-t`: Opsi ini berarti "TCP", yang akan menampilkan koneksi yang menggunakan protokol TCP. TCP adalah protokol yang umum digunakan untuk komunikasi data di internet.
    

**Kenapa pakai perintah ini?**

Perintah `netstat -at` sangat berguna untuk:

* **Memantau Koneksi**: Kita bisa melihat koneksi apa saja yang sedang aktif di komputer kita, dan ke mana saja komputer kita terhubung.
    
* **Mencari Masalah**: Kalau ada masalah dengan jaringan, kita bisa cek dengan perintah ini, mungkin ada koneksi yang mencurigakan atau koneksi yang bermasalah.
    
* **Keamanan**: Kita bisa melihat apakah ada koneksi yang tidak dikenal atau mencurigakan, yang mungkin menandakan adanya aktivitas yang tidak diinginkan.
    

## Task 4.9: Use netstat to display all ports that are in the listening state on your system.

```bash
netstat -l
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739175616380/6726d4f0-e183-42a9-94f7-cc7b285601c2.png align="center")

`-l`: Opsi ini berarti "listening", yang akan menampilkan *socket* yang sedang dalam status *listening*. Artinya, *socket* ini sedang menunggu koneksi dari komputer lain.

**Kenapa pakai perintah ini?**

Perintah `netstat -l` sangat berguna untuk:

* **Mencari Layanan yang Aktif**: Kita bisa melihat layanan atau program apa saja yang sedang berjalan di komputer kita dan menunggu koneksi masuk.
    
* **Keamanan**: Kita bisa melihat apakah ada *socket* yang *listening* yang tidak kita kenal atau mencurigakan, yang mungkin menandakan adanya aktivitas yang tidak diinginkan.
    
* ***Troubleshooting***: Kalau ada masalah dengan layanan jaringan, kita bisa cek dengan perintah ini, mungkin ada *socket* yang tidak seharusnya *listening* atau ada *socket* yang bermasalah.
    

## **Task 4.10: Use iperf in client mode to test the network bandwidth to a remote server (**[**iperf-server.google.com**](http://iperf-server.google.com)**).**

```bash
sudo apt install iperf
iperf -c iperf.he.net
```

* `iperf`: Ini adalah nama perintahnya, yaitu program `iperf` itu sendiri. `iperf` adalah *tool* yang sangat berguna untuk mengukur *bandwidth* atau kecepatan transfer data dalam jaringan.
    
* `-c`: Opsi ini adalah singkatan dari "client". Artinya, kita menjalankan `iperf` dalam mode *client*, yaitu sebagai pengirim atau penerima data.
    
* [`iperf.he.net`](http://iperf.he.net): Ini adalah alamat *domain* atau *hostname* dari server `iperf`. Dalam hal ini, kita menggunakan server `iperf` yang disediakan oleh Hurricane Electric (HE), sebuah penyedia layanan internet.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739175870835/b9e39959-4c7a-4786-9623-d12b503eb528.png align="center")

## **Task 4.11: Use iperf in server mode to listen for incoming connections on a remote machine (**[**iperf-server.discord.com**](http://iperf-server.discord.com)**).**

```bash
sudo apt install iperf3
iperf3 -s -p 8791
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739176844177/64d31e24-4d52-4349-9afd-db2919d586ac.png align="center")

* `-s`: Opsi ini adalah singkatan dari "server". Artinya, kita menjalankan `iperf3` dalam mode *server*, yaitu sebagai penerima data. Komputer yang menjalankan perintah ini akan menunggu koneksi dari *client*.
    
* `-p 8791`: Opsi ini digunakan untuk menentukan *port* yang akan digunakan oleh *server*. Secara *default*, `iperf3` menggunakan *port* 5201, tapi di sini kita mengubahnya menjadi 8791. Ini berguna jika *port* *default* sedang digunakan oleh aplikasi lain, atau jika kita ingin menjalankan beberapa *instance iperf3* secara bersamaan.
    

**and client**

```bash
iperf3 -c 192.168.1.14 -p 8791
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739176878877/df2ee921-e7c7-408f-b9ab-ffed2d859632.png align="center")

* `-c`: Opsi ini adalah singkatan dari "client". Artinya, kita menjalankan `iperf3` dalam mode *client*, yaitu sebagai pengirim data. Komputer yang menjalankan perintah ini akan terhubung ke *server*.
    
* `192.168.1.14`: Ini adalah alamat IP dari komputer yang menjalankan *server iperf3*. *Client* perlu mengetahui alamat IP *server* agar bisa terhubung.
    
* `-p 8791`: Opsi ini harus sama dengan *port* yang digunakan oleh *server*. Karena *server* menggunakan *port* 8791, maka *client* juga harus menggunakan *port* 8791.
    

## **Task 4.12: Use nc (Netcat) to check if port 443 is open on** [**discord.com**](http://discord.com)**.**

```bash
nc -zv discord.com 443
```

* `nc`: Ini adalah nama perintahnya, yaitu program `nc` atau netcat itu sendiri. `nc` adalah *tool* serbaguna yang bisa digunakan untuk berbagai macam tugas terkait jaringan, termasuk membuat koneksi TCP atau UDP, *listening* di *port* tertentu, dan lain-lain.
    
* `-z`: Opsi ini berarti "zero-I/O mode". Artinya, `nc` tidak akan mengirim atau menerima data apa pun. Kita hanya ingin tahu apakah koneksi bisa dibuat atau tidak.
    
* `-v`: Opsi ini berarti "verbose". Artinya, `nc` akan menampilkan informasi yang lebih detail tentang proses koneksi.
    
* [`discord.com`](http://discord.com): Ini adalah alamat *domain* atau *hostname* yang ingin kita tuju. Dalam hal ini, kita mau coba koneksi ke Discord.
    
* `443`: Ini adalah nomor *port*. *Port* 443 biasanya digunakan untuk HTTPS, yaitu protokol yang aman untuk *browsing* web.
    

## **Task 4.13: Use nc to send the message “Hello, Server!” to** [**google.com**](http://google.com) **on port 12345.**

```bash
#listening
nc -l 12345
#send message
echo "Hello, Server!" | nc localhost 12345
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739177163497/c5118e14-52c1-4781-aa11-638eeb99a129.png align="center")

Kedua perintah ini menunjukkan cara menggunakan `nc` (netcat) untuk komunikasi sederhana antar dua proses di Linux, mirip *client-server*. Perintah pertama menjalankan `nc` sebagai *server* yang "mendengarkan" di *port* 12345, dan perintah kedua menjalankan `nc` sebagai *client* yang mengirim pesan "Hello, Server!" ke *server* tersebut.

## **Task 4.14: Use nc to start a simple HTTP server on port 8080 and serve a static HTML file.**

```bash
echo "<html><body><h1>Hi, mdrdani</h1></body></html>" > index.html
```

`echo "<html><body><h1>Hi, mdrdani</h1></body></html>" > index.html`: Perintah ini membuat file `index.html` dan menuliskan kode HTML sederhana di dalamnya. Tanda `>` mengalihkan *output* dari `echo` ke file `index.html`.

```bash
while true; do (echo -e "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n"; cat index.html) | nc -l 8791; done
```

* `while true`: Ini adalah *loop* tak terbatas. Artinya, *web server* ini akan terus berjalan sampai kamu hentikan secara manual (biasanya dengan `Ctrl+C`).
    
* `do`: Ini menandai awal dari blok perintah yang akan diulang dalam *loop*.
    
* `(echo -e "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n"; cat index.html)`: Bagian ini membuat *response* HTTP yang akan dikirim ke *client*.
    
    * `echo -e "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n"`: Perintah `echo` dengan opsi `-e` digunakan untuk menampilkan teks dengan interpretasi *escape sequence*. Ini membuat *header* HTTP. `HTTP/1.1 200 OK` menandakan *response* sukses. `Content-Type: text/html` memberitahu *browser* bahwa kontennya adalah HTML. `\r\n` adalah *carriage return* dan *line feed*, yang menandai akhir baris dalam *header* HTTP. Dua baris kosong setelah *Content-Type* menandai akhir *header*.
        
    * `;`: Tanda titik koma memisahkan dua perintah dalam satu baris.
        
    * `cat index.html`: Perintah `cat` digunakan untuk membaca isi file `index.html`.
        
* `|`: Tanda pipa ini "menyalurkan" *output* dari perintah di dalam kurung ke perintah selanjutnya.
    
* `nc -l 8791`: Perintah `nc` (netcat) dijalankan dalam mode *listening* (`-l`) pada *port* 8791. Ini akan menerima koneksi dari *client* (misalnya, *browser*).
    
* `done`: Ini menandai akhir dari blok perintah yang diulang dalam *loop*.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739177497780/b96369a8-0766-45fa-8470-1f6fd24158fc.png align="center")

## **Task 4.15: Manually query for the domain** [**discord.com**](http://discord.com)**.**

```bash
sudo apt install bind9-dnsutils
dig @8.8.8.8 discord.com
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1739177727895/0946ae70-b173-4688-8aa2-f06417bba36b.png align="center")

`dig` (domain information groper) untuk mencari informasi DNS (Domain Name System) tentang [`discord.com`](http://discord.com) dengan bantuan *DNS server* publik Google di `8.8.8.8`.