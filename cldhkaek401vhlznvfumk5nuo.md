---
title: "Build a CI/CD workflow with Github Actions - Sesi 3"
seoTitle: "Build a CI/CD workflow with Github Actions - Sesi 2"
seoDescription: "Build a CI/CD workflow with Github Actions - Sesi 2"
datePublished: Sun Jan 29 2023 15:53:39 GMT+0000 (Coordinated Universal Time)
cuid: cldhkaek401vhlznvfumk5nuo
slug: deploy-docker-container-with-github-actions-sesi-3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/gy08FXeM2L4/upload/74fc9016f5274e381cdc89a241544d1c.jpeg
tags: docker, aws, go, github

---

### Github Actions Login to Docker Hub

Syncronize terlebih dahulu vscode kita ke github, nanti ada tambahan file YAML yang terbentuk dari membuat workflow Go di Github Actions.

Edit file **go.yaml**

Dokumentasi nya di ambil dari sini [Docker Build Push Action](https://github.com/docker/build-push-action).

```yaml
......

- name: Test
      run: go test -v ./...

- name: Login ke Docker Hub
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_HUB }}
```

pada bagian **variable** **username** kita belum simpan ke **github secret,** untuk **variable password** sudah kita simpan dengan nama **DOCKER\_HUB** cek [materi sebelum nya](https://mdrdani.my.id/deploy-docker-container-with-github-actions-session-2#heading-create-repository-secret-github),

jadi kita kembali ke **github &gt; settings &gt; Secrets and variables &gt; Actions**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674994031841/c415a7f1-7be5-4d9c-af09-0accc66cab96.png align="center")

jika sudah **add secret**

kembali ke vscode lakukan **git commit.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674996350319/7699bde1-5792-4d5f-ab76-ca09b8a94f88.png align="center")

lihat di **github** ke bagian menu **actions** ada proses **build**. cek apakah sukses untuk login ke docker hub.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674996395737/300d0366-7d0b-4cbe-b9b6-fc98b047ef1a.png align="center")

**Pada proses jobs build Login Success Ke Docker Hub**

### Github Actions Build and Push Docker Image

edit file **go.yaml** kembali

```yaml
.....

- name: Build and push docker image
  uses: docker/build-push-action@v3
  with:
    push: true
    tags: cehamot/golang-apiserver:${{ github.run_number }}
```

pada bagian tags kita coba ikuti **number dari github actions** karena jika menggunakan latest nanti akan error. oke lakukan **commit and push** kembali

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675000080301/119c3ea9-63f0-4230-863b-26054060959b.png align="center")

cek docker hub apakah sudah ada docker image.

Terlihat sudah ada image dengan **Tag 5** yang berarti hasil dari proses job Build **Github Actions.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675000199857/2047c487-c2c8-42fa-98e3-251b3d8fa870.png align="center")

sampai sini sukses untuk automate build and push docker image ke docker hub.

### Provisioning Docker Image to Server Instance AWS EC2

Pada tahap ini kita akan menyiapkan server untuk menjalankan image docker yang sudah kita letakkan di Docker Hub.

edit file **go.yaml** kembali.

Di sini akan di buatkan jobs baru dengan nama **provisioning-server.**

```yaml
......

jobs:
  build:
   .....

  provisioning-server:
```

untuk dapat terkoneksi antara github actions ke server instance berarti kita memerlukan SSH server, cara untuk konfigurasinya ada di dokumentasi [SSH for GitHub Actions](https://github.com/appleboy/ssh-action).

```yaml
.......
provisioning-server:
    runs-on: ubuntu-latest
    needs: build    
    steps:
    - name: Login ke Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_HUB }}
    - name: executing remote ssh deploy container
      uses: appleboy/ssh-action@v0.1.7
      with:
        host: ${{ secrets.SSH_IP }}
        username: ${{ secrets.SSH_USER }}
        key: ${{ secrets.SSH_KEY }}
        script: |
          docker pull cehamot/golang-apiserver:${{ github.run_number }}
          docker rm -f apiserver
          docker run --name apiserver -d -p 80:8144 cehamot/golang-apiserver:${{ github.run_number }}
```

pada bagian **variable script** merupakan command docker untuk

* mengambil (pull) image dari Docker Hub
    
* menghapus jika ada duplicate container (rm)
    
* dan jalankan (run).
    

oke lakukan **commit and push** untuk melihat apakah sukses proses **Job Provisioning Server**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675003395590/2b8016a6-1f32-4664-876c-64501fe1ae53.png align="center")

cek ke **server instance AWS EC2** apakah sudah running docker container nya.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675003675654/ea235772-77e9-4729-8ea9-bd2803bb08f9.png align="center")

cek menggunakan **browser**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675003714062/f3222187-f668-40a0-8b65-04c1207e4925.png align="center")

oke sukses

### Resize Docker Image

Docker image sekarang yang kita build memiliki size yang cukup besar ya mengakibatkan proses waktu deploy lumayan cukup lama.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675005580258/885cf0f8-0b21-4e77-9aeb-dda6ea325f1e.png align="center")

Bagaimana kalau kita resize Docker image nya? cara nya kita modify **Dockerfile** nya.

```dockerfile
FROM golang:alpine3.16 AS build
WORKDIR /app
COPY go.mod ./
COPY go.sum ./
RUN go mod download
COPY *.go ./
RUN go build -o /aplikasi

FROM alpine:latest
WORKDIR /
COPY --from=build /aplikasi /aplikasi
EXPOSE 8144
ENTRYPOINT ["/aplikasi"]
```

check docker hub repository,

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006184264/bcf3d5a2-de42-46bf-b2d2-717b8af5d395.png align="center")

berkurang sangat jauh yang tadi nya 100an MB menjadi 8.66 MB

### Create Branch Staging Github Actions

Biasa nya kalau di lapangan kan gak mungkin tuh langsung **push** ke **branch master/main**, biasanya ke **branch staging** atau **development** dahulu coba kita lakukan seperti itu ya.

edit file **main.go** terserah yang penting bisa kita commit dan buat branch baru kasih nama **staging.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006461670/f325b7ec-7b7f-4861-a257-7ec147b882a9.png align="center")

sampai di github terdeteksi ada notification ada branch baru yang di buat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006516103/87824ff3-2bc6-4d74-bfe2-a98f4c8d87bc.png align="center")

biasa nya kita **review** dulu tuh apa yang di push di **branch staging** baru kita **merge** ke branch **main**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006577550/d95511e6-8de3-4dbe-9040-a4d115c9d7cc.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006597237/3a1192d1-78f8-4cf6-b6a4-10c37e5075cb.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006733759/85c8146f-22bc-41e6-83e9-497e1f69d4d8.png align="center")

hasil nya yang sudah kita edit.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675006831824/69ccf2be-be87-4f65-b23a-b3c733244516.png align="center")

DONE !!