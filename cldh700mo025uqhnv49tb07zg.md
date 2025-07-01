---
title: "Build a CI/CD workflow with Github Actions - Sesi 2"
seoTitle: "Build a CI/CD workflow with Github Actions - Sesi 2"
seoDescription: "Build a CI/CD workflow with Github Actions - Sesi 2"
datePublished: Sun Jan 29 2023 09:41:39 GMT+0000 (Coordinated Universal Time)
cuid: cldh700mo025uqhnv49tb07zg
slug: deploy-docker-container-with-github-actions-sesi-2
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/6sAl6aQ4OWI/upload/c7a02e8da51c8f6efbc7a1c79bd3e5f7.jpeg
tags: docker, go, github

---

### Create Dockerfile

lihat dokumentasi [Docker Images Golang](https://docs.docker.com/language/golang/build-images/) untuk membuat Dockerfile.

```dockerfile
FROM golang:alpine3.16

WORKDIR /app

COPY go.mod ./
COPY go.sum ./
RUN go mod download

COPY *.go ./

RUN go build -o /aplikasi

EXPOSE 8144

ENTRYPOINT ["/aplikasi"]
```

kita akan mencoba test build dan running di lokal terlebih dahulu apakah aplikasi berjalan dengan baik atau tidak.

```bash
$ go build -o aplikasi
$ ./aplikasi
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /                         --> main.main.func1 (3 handlers)
[GIN-debug] [WARNING] You trusted all proxies, this is NOT safe. We recommend you to set a value.
Please check https://pkg.go.dev/github.com/gin-gonic/gin#readme-don-t-trust-all-proxies for details.
[GIN-debug] Listening and serving HTTP on 0.0.0.0:8144
```

selanjut nya build image aplikasi nya.

```bash
$ docker build . -t golang-apiserver
$ docker images
REPOSITORY                        TAG       IMAGE ID       CREATED          SIZE
golang-apiserver                  latest    3eb6a831e1da   30 seconds ago   529MB
```

coba test running kembali

```bash
$ docker run -itd -p 8144:8144 golang-apiserver
```

### Create Repository Docker Hub & Access Token

Login ke [Docker Hub](https://hub.docker.com/), login atau sign up jika belum memiliki akun nya. Ketika sudah login kita buat satu repository dengan nama golang-apiserver.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674966623029/a5900231-3c5c-452a-b893-5134b51514e9.png align="center")

untuk mengakses Docker hub dari Github Actions kita memerlukan acccess token yang di generate dalam Docker Hub.

Pilih menu **Account Settings &gt; Security &gt; New Access Token.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674966824894/729fd0f0-da25-47f0-971e-c2f1e46763b9.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674966915196/1c030a29-156b-4ede-925b-2984110daee4.png align="center")

**Copy Token** yang sudah di buat.

### Create Repository Secret Github

Selanjutnya kembali ke **github &gt; select repository &gt; settings&gt; Secrets and variables.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674967272446/10e824bb-40fd-485e-8132-29e13d05642c.png align="center")

pilih **New repository secret**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674967459582/e7075382-87a7-46b1-9fea-da9410616701.png align="center")

pada bagian secret **paste access token** yang sudah di berikan di Docker Hub. Access Token ini seperti password untuk masuk ke akun Docker Hub milik kita.

Selanjutnya **create new repository secret** lagi namun secret yang dari **key server instance AWS EC2**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674967739802/0e591693-6c8a-41ef-a384-ade25df9a516.png align="center")

**create repository secret** lagi untuk ssh user.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674968149481/c5c92414-186c-43b0-8e75-9536986d9498.png align="center")

**create repository secret** lagi untuk **ssh ip**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675003550191/88ba25a9-a338-450a-b8af-54dedf710dbc.png align="center")

### Setup Github Actions

Masuk ke menu **Actions** Pilih Go untuk memulai Github Actions. untuk mempelajari github actions workflows bisa ke dokumentasi ini [Github Workflows Trigger](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674971881027/01de1e00-4f21-4b8e-b26c-139a2e202213.png align="center")

ada sedikit perubahan dari file **go.yml**

```yaml
# This workflow will build a golang project
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-go

name: Go

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Set up Go
      uses: actions/setup-go@v3
      with:
        go-version: 1.19

    - name: Test
      run: go test -v ./...
```

start commit

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674972307034/3a91753b-9daa-449b-8954-93a2a2e3b410.png align="left")

jika sudah commit bakalan ada 1 actions workflow yang berjalan, bisa di lihat ke menu **Actions**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674984304791/9f9074c9-fb13-4c59-8371-15fe0f598437.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674984409421/d01d2578-d539-4e53-ab5c-12a73fa946e3.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674984561110/db927d8b-73c4-4c8e-a447-66b85fd3579d.png align="center")

sampai sini masih sampai base bawaan next kita akan **build and push container** nya.