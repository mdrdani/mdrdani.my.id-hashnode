---
title: "Terraform Fundamental - Sesi 1 (AWS)"
seoTitle: "Terraform Fundamental - Sesi 1 (AWS)"
seoDescription: "Terraform Fundamental - Sesi 1 (AWS)"
datePublished: Mon Dec 19 2022 14:12:55 GMT+0000 (Coordinated Universal Time)
cuid: cld7gmcn3000g09jufk10594m
slug: terraform-practice-session-1-aws
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/klWUhr-wPJ8/upload/e6357108ef1bbedc60431e72fa6f3d58.jpeg
tags: aws, terraform

---

Di bagian introduction dan installation kita sudah mengetahui apa itu terraform dan bagaimana instalasi nya di windows untuk di gunakan melalui CMD. next tahap part 2 ini kita akan mencoba konfigurasi Terraform dari tahap **Plan**, **Apply,** sampai dengan **Destroy.**

### **Prepare**

Hal-hal yang perlu disiapkan untuk ngelab adalah sebagai berikut :

* Install Terraform
    
* Code editor : VScode
    
* Extensions VScode : Terraform, Hashicorp Terraform
    
* AWS Account
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396510888/97bfcc32-1b86-474c-9742-08601e4cf2d5.webp align="center")

### **Setting Provider**

untuk setting provider kita tinggal copy paste saja dari situs [https://registry.terraform.io/providers/hashicorp/aws/latest](https://registry.terraform.io/providers/hashicorp/aws/latest), semua dokumentasi yang di butuhkan untuk membangun infrastruktur terutama AWS sudah tersedia semua di sana.

Buka VSCode nya dan buat folder kita namain saja **Terraform,** dan buat file dengan nama [**provider.tf**](http://provider.tf)

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "4.48.0"
    }
  }
}

provider "aws" {
  # Configuration options
  region = "ap-southeast-3"
﻿  }
```

selanjutnya kita akan jalankan command **terraform init**, yang gunanya akan mendownload provider yang kita setting menggunakan **AWS.**

```bash
$ terraform init
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396548814/c5410749-c8f2-48ac-aafa-ebd8f8dc2635.jpeg align="center")

jika sudah nanti struktur folder (provider) akan seperti ini

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396563973/9844ffbd-0526-4b1c-921f-f71c70e0ffbe.jpeg align="center")

### **Setting Resource (EC2)**

buat file [**ec2.tf**](http://ec2.tf) di folder **Terraform,** isi file [**ec2.tf**](http://ec2.tf) dengan :

```bash
resource "aws_instance" "VM-Node" {
  ami           = "ami-0a2bcdce90f0df342"
  instance_type = "t3.micro"


  tags = {
    Name = "VM-Node"
  }
}
```

untuk mengetahui **ID AMI** nya kita harus masuk ke **AWS console** terlebih dahulu ke bagian **EC2** dan **launch instance** nanti pilih AMI nya di bagian bawah ada **AMI ID**.

### **Setting Credential**

Sebelum itu kita harus setting credential nya dahulu, bisa kita setting melalui **AWS Console &gt; IAM &gt; Users.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396595755/9b2a6f6b-6720-4a65-963e-6f5e5e7d3ac5.jpeg align="center")

Pilih ke tab **Security Credentials**, scroll ke bawah pilih **Create Access Key.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396622735/e6ceb3ff-f80b-4a22-ba02-6dbbf7864f7d.jpeg align="center")

Setelah kita create access key kita modify kembali file [**provider.tf**](http://provider.tf) dengan menambahkan

```bash
........

provider "aws" {
  # Configuration options
  region = "ap-southeast-3"
  ﻿ access_key = "ACCESS KEY YANG TADI DI GENERATE"
   secret_key = "﻿﻿SECRET KEY YANG TADI DI GENERATE"
﻿ }
```

dan setelah itu kita jalankan command

```bash
$ terraform plan
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396649783/8945bd10-a4b1-485d-8d1c-02ab0269d0b2.jpeg align="center")

nah jika sudah muncul seperti ini berarti sudah bisa untuk stage selanjutnya.

```bash
$ terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396670787/9e3154d2-cea2-4194-ac01-f82458b8add1.jpeg align="center")

dan Success EC2 Instance sudah terbuat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396697707/b2496268-f451-4163-a161-48da0f6e77be.jpeg align="center")

Jika ingin mengahapus EC2 instance nya yang sudah terbuat, dapat menggunakan command

```bash
$ terraform destroy
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674396722269/0bdd2cef-8acf-4b5e-8320-212b74da224d.jpeg align="center")