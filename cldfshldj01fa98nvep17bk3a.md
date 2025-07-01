---
title: "Build a CI/CD workflow with Github Actions - Sesi 1"
seoTitle: "Build a CI/CD workflow with Github Actions - Sesi 1"
seoDescription: "Build a CI/CD workflow with Github Actions - Sesi 1"
datePublished: Sat Jan 28 2023 10:07:38 GMT+0000 (Coordinated Universal Time)
cuid: cldfshldj01fa98nvep17bk3a
slug: deploy-docker-container-with-github-actions-session-1
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/842ofHC6MaI/upload/fedd61629b0f1846d4b8f54ee2dcbfe6.jpeg
tags: docker, aws, github

---

### Prerequisite

* Account Docker Hub
    
* Account Github
    
* Account AWS
    
* Ansible
    
* Terraform
    
* Golang
    
* Docker
    
* VS Code
    

### Arsitektur Diagram

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674709943148/273bf0a2-0d0c-4307-b50d-bce064f64b71.jpeg align="center")

### Create Instance AWS EC2

Buat sebuah server di AWS EC2 bisa create langsung melalui console panel AWS atau menggunakan terraform. Untuk konfigurasi menggunakan terraform sudah diberikan pada artikel [Ansible Practice - Session 1](https://mdrdani.my.id/ansible-practice-session-1#heading-create-vm-ec2-aws).

### Install Docker on Instance AWS EC2

Instance AWS EC2 memakai ubuntu 22.04, lihat di dokumentasi [install docker on ubuntu 22.04](https://docs.docker.com/engine/install/ubuntu/).

jika sudah menginstall Docker di Instance AWS EC2 lalu masukan user **<mark>Ubuntu</mark>** ke group **<mark>docker.</mark>**

```bash
$ sudo usermod -aG docker ubuntu
```

exit terlebih dahulu dari terminal dan remote kembali. check apakah docker sudah running di instance AWS EC2 nya.

```bash
$ docker ps
atau
$ docker container ls -a
```

### Create App Golang

buat folder untuk aplikasi golang dan buka menggunakan <mark>VSCode</mark>

```bash
$ go mod init github.com/****/apiserver
```

buat file <mark>main.go</mark>

```go
package main

import "fmt"

func main() {
	fmt.Println("Server running")
}
```

test apakah running

```bash
$ go run .
Server running
```

selanjutnya install Framework Golang yaitu <mark>Gin</mark>

```bash
$ go get github.com/gin-gonic/gin
```

edit file <mark>main.go</mark>

```go
package main

import (
	"os"

	"github.com/gin-gonic/gin"
)

func main() {
	hostname, _ := os.Hostname()
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"code":    200,
			"message": "api server",
			"server":  hostname,
		})
	})
	r.Run("0.0.0.0:8144")
}
```

coba running

```bash
$ go run .
```

dan akses ke <mark>localhost:8144</mark> menggunakan web browser atau bisa juga melalui <mark>Postman.</mark>

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674802318834/d56362eb-03a1-4269-98f5-34bc9d5984c0.png align="center")

### Create Repository Github

buat repository di github untuk mengupload Aplikasi Golang yang tadi sudah dibuat .

```bash
$ git add .
$ git commit -m "push app golang"
[master (root-commit) d207657] push app golang
 3 files changed, 129 insertions(+)
 ......
$ git branch -M main
$ git remote add origin https://github.com/mdrdani/container-app.git
$ git push -u origin main
Enumerating objects: 5, done.
......
```