---
title: "Introduction to Container and Docker"
seoTitle: "Introduction to Container and Docker"
seoDescription: "Introduction to Container and Docker"
datePublished: Thu Jan 19 2023 14:36:59 GMT+0000 (Coordinated Universal Time)
cuid: cld7hh4rp000109mpc87h5uju
slug: introduction-to-container-and-docker
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/jOqJbvo1P9g/upload/c1834ae6791ad2f9ff3dfe537917ff08.jpeg
tags: docker

---

Pada tulisan kali ini kita akan mengenal dahulu konsep VM dan Kontainer agar dapat mudah di pahami kita bahas satu persatu dari

### **Virtual Machine / VM**

* VM adalah teknologi untuk optimasi kapasitas server
    
* VM bisa running banyak komputer dalam satu server fisik
    
* Penggunaan resource akan dibagi ke sejumlah VM yang ada dan bisa di atur
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674398132415/8bda955f-4006-4495-86dc-7a0d676a8659.jpeg align="center")

Hypervisor adalah virtual machine monitor (VMM) yang bertugas untuk membuat, menjalankan, dan mengatur VM. Hypervisor dibagi menjadi 2:

* Type-1 hypervisor (native atau bare-metal hypervisor)
    
* Type-2 hypervisor (hosted hypervisor)
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674398150736/c46ef533-737c-4c8d-956e-834f895eab1d.jpeg align="center")

Hypervisor tugasnya adalah mem-virtualisasi VM. Software untuk membuat Virtual Machine contoh nya : HyperV Windows, Virtual Box, Proxmox, VM Workstation.

### **Containerization**

Containerization adalah konsep virtualisasi virtual, yang disebut dengan container (bukan VM). Container ini lebih ringan dan fungsinya lebih spesifik. adapun kelebihan container di banding VM adalah

* less overhead
    
* mudah untuk di deploy
    
* efisien, mudah untuk di patch dan scale
    
* dll....
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674398171324/aea430ee-bc75-40b8-ad77-f2559e243fc9.png align="center")

### **Docker**

Docker merupakan platform untuk virtualisasi di level OS. Virtual pada docker disebut dengan container. Pada docker ada beberapa pengertian penting yang harus di ketahui

* Docker Container adalah virtual container yang isinya mencakup semua kebutuhan yang di perlukan untuk menjalankan aplikasi.
    
* Docker engine adalah merupakan software yang bertanggung jawab untuk containerization.
    
* Docker image adalah file hasil operasi build terhadap aplikasi yang ingin di dockerize. File image ini nantinya menjadi basis pembuatan dan ekekusi container
    
* Dockerfile adalah file text yang isinya semua command untuk keperluan build image.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674398190712/5b753ccb-b643-49ae-bb18-0646f7c9a9c4.jpeg align="center")