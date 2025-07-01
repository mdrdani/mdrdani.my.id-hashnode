---
title: "Terraform Introduction and Installation"
seoTitle: "Terraform Introduction and Installation"
seoDescription: "Terraform Introduction and Installation"
datePublished: Sun Dec 18 2022 13:27:24 GMT+0000 (Coordinated Universal Time)
cuid: cld7ezr8a00000amj7nbm3cd8
slug: terraform-introduction-and-installation
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/M5tzZtFCOfs/upload/fc4dc5d693b9b7d6cac4aac7f503947d.jpeg
tags: terraform

---

Pada kalangan DevOps / SRE tidak asing lagi dengan tools yang satu ini Terraform merupakan tools Infrastructure as Code untuk mempercepat dalam mengelola cloud seperti AWS, GCP, atau Azure namun tidak hanya cloud kita juga dapat menggunakan terraform untuk memanage Virtualisasi seperti Proxmox ataupun Container Docker. Terraform adalah tool open source yang dibuat oleh Hashicorp. Dengan terraform kita dapat membuat, mengubah, menghapus dan menduplikasikan infrastructure.

### **Core Terraform Workflow**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393759006/c98f1aa3-d68a-4870-9f3d-5fea8368c03d.avif align="center")

**Write** : menulis terraform configuration dan initialize

semua konfigrasi terdokumentasi rapih di [https://registry.terraform.io/](https://registry.terraform.io/)

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Create a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.0.0.0/16"
}
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Create a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.0.0.0/16"
}
```

**Plan :** Preview

Sebelum dieksekusi ke infrastuk kita bisa melakukan preview terlebih dahulu apa yang akan di buat, ubah atau hapus sehingga bisa lebih aman saat di eksekusi ke infrastuktur.

**Apply :** Execution

Setelah tahap plan (preview) maka kita bisa eksekusi ke infrastukture yang sebenarnya.

### **Terraform Command**

cukup banyak command yang dimiliki oleh terraform yang bisa digunakan, namun untuk awal kita hanya cukup mengetahui beberapa command yang biasa di pakai. Berikut ini beberapa command dasar :

* * *terraform init =&gt; inisialisasi*
        
    * *terraform fmt=&gt; merapihkan konfigurasi code file*
        
    * *terraform validate =&gt; check apakah konfigurasi code sudah sesuai*
        
    * *terraform plan =&gt; check apa saja yang akan di rubah*
        
    * *terraform apply =&gt; menerapkan perubahan*
        
    * *terraform destroy=&gt; menghapus*
        

### **Install Terraform**

Download Terafform di situs ini [https://developer.hashicorp.com/terraform/downloads](https://developer.hashicorp.com/terraform/downloads). Pilih operating system menggunakan apa, di sini saya menggunakan windows.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393925368/5e29778c-76b2-465e-86b7-e977fdc404f0.jpeg align="center")

Buka file zip nya copy **terraform.exe** ke drive C: dengan membuat folder terraform.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393944477/066779bd-3180-47b7-98ff-2878d6d8dd8c.jpeg align="center")

Update system global path pada windows di bagian **Control Panel -&gt; System -&gt; System Settings-&gt; Environment Variable**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393965767/aed79b41-12bb-4110-b4c5-760a01e54925.jpeg align="center")

**Oke Save,** dan test buka CMD kembali.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393994650/dd114a92-48d0-4be6-a132-340d548f5c65.jpeg align="center")

Sukses terraform sudah terinstall di windows.