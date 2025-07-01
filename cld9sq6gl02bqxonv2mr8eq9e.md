---
title: "Docker Fundamental - Sesi 2"
seoTitle: "Docker Practice - Session 2"
seoDescription: "Docker Practice - Session 2"
datePublished: Tue Jan 24 2023 05:27:42 GMT+0000 (Coordinated Universal Time)
cuid: cld9sq6gl02bqxonv2mr8eq9e
slug: docker-fundamental-sesi-2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1674610809019/f45d16df-c041-45ab-966f-653740b4205f.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1674537485534/b1b58b1f-c63c-4fcb-a93b-9b7e1bfac1ae.jpeg
tags: docker, go

---

Lanjut dari [Sesi 1](https://mdrdani.my.id/docker-practice-session-1) sebelumnya

### Image Tagging and Manajemen Container

Docker image bisa kita namai sesuai keinginan melalui **flag -t**

```bash
$ docker build . -t namaimage
```

secara default docker image menggunakan **Tag latest,** kita dapat mengcustom **Tag** dengan menambahkan nya di belakang nama image

```bash
$ docker build .  -t namaimage:tag
```

Example :

```bash
$ docker build . -t my-app-image:v1
```

jika **image** sudah terbentuk selanjutnya kita akan membuat **container**, untuk melihat **list container** dapat menggunakan command

```bash
$ docker container ls --all
or
$ docker container ls -a
```

untuk melihat **list image** menggunakan command

```bash
$ docker images
```

untuk **create container** baru dapat menggunakan command

```bash
$ docker container create -e PORT=8083 -p 8083:8083 my-app-image
```

untuk **run container** menggunakan command

```bash
$ docker container start *container-id*
```

untuk **stop container** menggunakan command

```bash
$ docker container stop *container-id*
```

command **docker run** juga dapat seperti **Docker container create** dengan menambahkan **flag -d.**

```bash
$ docker run -itd -e PORT=8083 -p 8083:8083 my-app-image
```

next untuk **menghapus container** dapat menggunakan command. sebelum di hapus container pastikan container sudah di stop.

```bash
$ docker container rm *container-id*
```

jika container sedang berjalan, kita akan paksa untuk berhenti dan di hapus bisa menggunakan **flag -f** yang artinya force.

```bash
$ docker container rm -f *container-id*
```

### Docker Build Arguments dan Container Environment Variables

Dalam pembuatan docker container kita menggunakan beberapa **environment variables** lewat **docker run** maupun **docker container create.** Ada satu lagi di sebut dengan **BuildArguments** di sisipkan saat build **docker image**.

```bash
#Example :
$ docker run -it -e PORT=8083 -p 8083:8083 my-app-image
```

<mark>PORT=8083</mark> itu yang di sebut dengan **environment variable** dengan **<mark>flag -e</mark>**.

ada cara untuk mempermudah agar kita tidak selalu definisikan environment variable pada saat docker run, yaitu tambahkan baris di **Dockerfile.**

silahkan edit file **Dockerfile**

```bash
.....
RUN apk update && apk add git

#environment variable
ENV PORT=8083
ENV INSTANCE_ID="dockerfile"

#menetapkan direktori kerja saat ini
WORKDIR /app

.......
```

kita coba lagi **Build image docker**

```bash
$ docker build . -t my-app-image-environment

#flag -e hilang
$ docker run -it -p 8083:8083 my-app-image-environment
```

jika ada perubahan dari **environment variable** maka tinggal di sisipkan kembali **flag-e PORT dan INSTANCE\_ID** saat **docker run.**

next bagaimana kalau **Build Arguments,** perubahan dilakukan pasti nya melalui **Dockerfile** sebelum image terbentuk.

```bash
.....
RUN apk update && apk add git

#build arguments
ARG DEFAULT_PORT==7777

#environment variable
ENV PORT=${DEFAULT_PORT}
ENV INSTANCE_ID="dockerfile"

#menetapkan direktori kerja saat ini
WORKDIR /app

.......
```

jadi saat akan build image kita menambahkan **flag --build-arg.**

```bash
#build
$ docker build . -t my-app-image-build --build-arg DEFAULT_PORT=7073

#run
$ docker run -it -p 7073:7073 my-app-image-build
```

jika saat docker build tidak diset default\_port, maka kita dapat set default\_port terlebih dahulu.

**Dockerfile**

```bash
.....
RUN apk update && apk add git

#build arguments
ARG DEFAULT_PORT=7777
ARG DEFAULT_INSTANCE_ID=9024

#log
RUN echo "isi dari argument DEFAULT_PORT adalah ${DEFAULT_PORT}"
RUN echo "isi dari argument DEFAULT_INSTANCE_ID adalah ${DEFAULT_INSTANCE_ID}"

#menetapkan direktori kerja saat ini
WORKDIR /app

.......
```

### Push Image Ke Docker Hub Registry

[Docker Hub](https://hub.docker.com/) adalah tempat repository dari image yang sudah di buat. Ada jutaan image yang di push kesana, ada yang di labeli **<mark>Docker Official Image</mark>** jika image tersebut dari sumber asli image itu terbentuk. Seperti github jika ingin mempush ke Docker hub maka kita akan create terlebih dahulu Repository.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674536832287/21f34076-44dc-42d5-ac1b-d698ac9c8cb1.png align="center")

sebelum push kita harus login dahulu

```bash
$ docker login --username=**** --password=***
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded

Logging in with your password grants your terminal complete access to your account. 
For better security, log in with a limited-privilege personal access token. Learn more at https://docs.docker.com/go/access-tokens/
```

jika success selanjutnya kita akan membuild image untuk diletakkan di Docker Hub. Biasanya pada saat build nama image di sesuaikan dengan repository di Docker Hub.

```bash
$ docker build . -t namauser/namaimage

#example
$ docker build . -t cehamot/my-app-image-golang
$ docker images
REPOSITORY                    TAG       IMAGE ID       CREATED             SIZE
cehamot/my-app-image-golang   latest    e66f29f39790   6 seconds ago       372MB
```

selanjutnya push image nya ke Docker Hub.

```bash
$ docker push namauser/namaimage:tag

#example
$ docker push cehamot/my-app-image-golang:latest
The push refers to repository [docker.io/cehamot/my-app-image-golang]
7bfbd225c323: Pushed 
934bc53c048b: Pushed 
7d08ff64a68b: Pushed 
43c144982db8: Pushed 
305bde9db59c: Pushed 
f4cb025061ce: Pushing [==================================================>]  11.06MB
5739252b30ab: Mounted from library/golang
```