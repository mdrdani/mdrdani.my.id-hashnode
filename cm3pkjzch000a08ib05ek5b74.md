---
title: "Traefik as Reverse Proxy & Load Balancer Locally"
datePublished: Wed Nov 20 2024 07:35:36 GMT+0000 (Coordinated Universal Time)
cuid: cm3pkjzch000a08ib05ek5b74
slug: traefik-as-reverse-proxy-load-balancer-locally
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/9BZX2ctaRPM/upload/273636bde59a2c6fea5b867a701ad90e.jpeg
tags: docker, load-balancer, reverse-proxy

---

![Architecture](https://doc.traefik.io/traefik/assets/img/traefik-architecture.png align="left")

### Tentang Traefik

**Traefik** adalah *reverse proxy* dan *load balancer* modern yang dirancang untuk mengelola lalu lintas web di dalam infrastruktur berbasis *microservices*. Traefik mendukung integrasi langsung dengan berbagai sistem orkestrasi seperti Docker, Kubernetes, dan lainnya, serta menyediakan fitur-fitur canggih untuk memudahkan pengelolaan lalu lintas aplikasi.

### Apa itu Reverse Proxy

*Reverse proxy* adalah server yang duduk di depan satu atau lebih server backend. Tugasnya adalah menerima permintaan dari klien (misalnya, browser pengguna), meneruskannya ke server backend yang sesuai, dan mengirimkan respons kembali ke klien. Dengan *reverse proxy*, kita bisa:

* Menyembunyikan detail server backend.
    
* Membagi beban lalu lintas ke beberapa server backend.
    
* Menambahkan fitur keamanan seperti TLS/SSL.
    

Traefik bertindak sebagai *reverse proxy* untuk layanan-layanan Anda.

### Fitur Utama Traefik

#### a. **Integrasi Otomatis dengan Orkestrasi Kontainer**

Traefik dapat secara otomatis mendeteksi dan mengatur rute untuk layanan yang berjalan di dalam sistem orkestrasi seperti Docker, Kubernetes, Nomad, atau Swarm. Anda tidak perlu secara manual mendefinisikan rute atau memuat ulang konfigurasinya.

#### b. **TLS Termination (HTTPS)**

Traefik mendukung *TLS termination*, yaitu proses mendekripsi lalu lintas HTTPS di sisi Traefik sebelum diteruskan ke server backend. Ini memungkinkan backend Anda tetap berjalan di HTTP biasa. Traefik juga mendukung integrasi dengan Let's Encrypt untuk mengelola sertifikat TLS otomatis.

#### c. **Routing Dinamis**

Traefik memungkinkan Anda untuk mendefinisikan rute secara dinamis menggunakan berbagai aturan:

* Berdasarkan host (contoh: `Host(`[`app.example.com`](http://app.example.com)`)`).
    
* Berdasarkan jalur (contoh: `Path(/api)`).
    
* Kombinasi aturan lainnya.
    

#### d. **Middlewares**

Middlewares memungkinkan Anda untuk memodifikasi lalu lintas sebelum diteruskan ke server backend. Contoh middleware:

* **Redirect Scheme**: Mengarahkan HTTP ke HTTPS.
    
* **Rate Limiting**: Membatasi jumlah permintaan.
    
* **Authentication**: Menambahkan otentikasi dasar untuk layanan tertentu.
    

#### e. **Load Balancing**

Traefik mendukung berbagai algoritma *load balancing* (round-robin, *least connections*, dll.) untuk mendistribusikan lalu lintas ke beberapa server backend.

#### f. **Dashboard**

Traefik menyediakan dashboard berbasis web yang memungkinkan Anda memantau status rute, middleware, dan layanan.

#### g. Plug-and-Play

Traefik mendukung banyak protokol seperti HTTP, HTTPS, TCP, dan UDP secara native, sehingga sangat fleksibel.

### Setup Traefik in docker-compose

docker-compose.yaml

```yaml
version: '3.9'

services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    command:
      - '--api.insecure=true' # Enables the Traefik dashboard
      - '--providers.docker=true'
      - '--providers.file.directory=/etc/traefik/dynamic'
      - '--entrypoints.web.address=:80'
      - '--entrypoints.websecure.address=:443'
      - '--entrypoints.websecure.http.tls=true'
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./certs:/certs
      - ./dynamic:/etc/traefik/dynamic
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.traefik.rule=Host(`traefik.docker.localhost`)'
      - 'traefik.http.routers.traefik.service=api@internal'
      - 'traefik.http.routers.traefik.tls=true'
    networks:
      - frontend
    restart: always

  nginx:
    image: nginx:latest
    container_name: nginx
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.nginx.rule=Host(`app.docker.localhost`)'
      - 'traefik.http.routers.nginx.entrypoints=websecure'
      - 'traefik.http.routers.nginx.tls=true'
      - 'traefik.http.services.nginx.loadbalancer.server.port=80'
    networks:
      - frontend
    restart: always

  nginxtwo:
    image: nginx:latest
    container_name: nginxtwo
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.nginxtwo.rule=Host(`apptwo.docker.localhost`)'
      - 'traefik.http.routers.nginxtwo.entrypoints=websecure'
      - 'traefik.http.routers.nginxtwo.tls=true'
      - 'traefik.http.services.nginxtwo.loadbalancer.server.port=80'
    networks:
      - frontend
    restart: always

networks:
  frontend:
    external: true
```

buat networks agar semua aplikasi menjadi satu network

```bash
$ docker network create frontend
```

selanjutnya kita setting domain localhost

```bash
$ sudo nano /etc/hosts
```

tambahkan domain local

* **traefik.docker.localhost**
    
* **app.docker.localhost**
    
* **apptwo.docker.localhost**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732086296948/23d468a5-900d-4a3d-8c00-08ccf10b7623.png align="center")

### Adding SSL certificates for [localhost](http://localhost)

Jika sudah setting domain local, selanjutnya generate local SSL dengan **mkcert.**

untuk ubuntu dapat menginstall dengan

```bash
$ sudo apt install mkcert libnss3-tools
```

```bash
$ mkdir cert
$ cd cert
$ mkcert -install
$ mkcert traefik.docker.localhost
$ mkcert app.docker.localhost
$ mkcert apptwo.docker.localhost
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732086712920/84c24d76-55b4-4c7a-8ed1-04038fbe0ddc.png align="center")

### Basic Traefik configuration walkthrough

selanjutnya kita buat dynamic configuration file.

```bash
$ mkdir dynamic
$ touch tls.yml
```

```yaml
# dynamic/tls.yml
tls:
  certificates:
    - certFile: /certs/traefik.docker.localhost.pem
      keyFile: /certs/traefik.docker.localhost-key.pem
    - certFile: /certs/app.docker.localhost.pem
      keyFile: /certs/app.docker.localhost-key.pem
    - certFile: /certs/apptwo.docker.localhost.pem
      keyFile: /certs/apptwo.docker.localhost-key.pem
```

### Run Traefik

```bash
//jika ingin melihat log tidak berjalan di background
$ docker compose up
//jika ingin lgsung berjalan di background
$ docker compose up -d
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732087440285/79db5588-4d07-477d-8eec-dad7ce709337.png align="center")

kita check di browser

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732087828139/cb4f3d74-ca1a-4621-bd2e-514afc16256e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732087874026/a5350f15-1ba2-4ffa-9259-30c3796cf627.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1732087888272/0fda6508-f91e-4d3b-b08c-0d261f4697b4.png align="center")

thanks…