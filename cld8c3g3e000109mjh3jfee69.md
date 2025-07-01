---
title: "Terraform Automation with Github & Atlantis - Session 3"
seoTitle: "Terraform Automation with Github & Atlantis - Session 3"
seoDescription: "Terraform Automation with Github & Atlantis - Session 3"
datePublished: Thu Dec 29 2022 04:54:06 GMT+0000 (Coordinated Universal Time)
cuid: cld8c3g3e000109mjh3jfee69
slug: terraform-automation-with-github-atlantis-session-3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/F_m44ut3XTw/upload/fb4c885676d9686b3e4e29d6906d0393.jpeg
tags: aws, terraform, terragrunt

---

### Create Module and Resources

oke kita akan create module dan resources, sebagai contoh di sini akan membuat VPC module dan VPC Resources. Untuk membuat file module dan resources kita akan selalu melihat [Terraform Document](https://registry.terraform.io/providers/hashicorp/aws/latest/docs). Struktur folder nanti akan seperti ini :

* module
    
    * vpc
        
        * main.tf
            
        * output.tf
            
        * vars.tf
            
* resources
    
    * staging
        
        * terragrunt.hcl
            
        * vpc
            
            * vpc-test-1
                
                * terragrunt.hcl
                    

### Create Module/VPC

[**main.tf**](http://main.tf)

```bash
terraform {
  backend "s3" {}
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "4.48.0"
    }
  }
}

provider "aws" {
  region                   = var.region
  shared_credentials_files = var.shared_credentials_files
  profile                  = var.profile
}

resource "aws_vpc" "default" {
  cidr_block = var.cidr_block

  tags = {
    Name = var.name
  }
}

resource "aws_subnet" "default" {
  vpc_id     = aws_vpc.default.id
  cidr_block = var.cidr_block_subnet[count.index]
  count      = length(var.cidr_block_subnet)

  tags = {
    Name = "${var.name}-subnet-${count.index + 1}"
  }
}
```

[**output.tf**](http://output.tf)

```bash
output "vpc_id" {
  value = aws_vpc.default.id
}

output "subnet_id" {
  value = aws_subnet.default.*.id
}
```

[**vars.tf**](http://vars.tf)

```bash
variable "region" {
  type = string
}

variable "shared_credentials_files" {
  type = list(any)
}

variable "profile" {
  type = string
}

variable "cidr_block" {
  type = string
}

variable "name" {
  type = string
}

variable "cidr_block_subnet" {
  type = list(any)
}
```

### Create Resources/staging/

**terragrunt.hcl**

```bash
locals {
    region = "ap-southeast-3"
    shared_credentials_file = "/home/ubuntu/.aws/credentials > folder credentials"
    profile = "nama profile di aws-cli credentials"
}

remote_state {
    backend = "s3"
    config = {
            bucket         = "nama bucket"
            key            = "${path_relative_to_include()}"
            region         = local.region
            encrypt        = true
            shared_credentials_file = local.shared_credentials_file
            profile = local.profile
  }
}

inputs = {
    region = local.region
    shared_credentials_files = [ local.shared_credentials_file]
    profile = local.profile
    name = "${basename(get_terragrunt_dir())}"
    ami = "ami-0a2bcdce90f0df342 *sesuaikan"
}
```

**vpc/vpc-test-1/terragrunt.hcl**

```bash
terraform {
    source = "../../../../module/vpc"
}

include {
    path = find_in_parent_folders()
}

inputs = {
    cidr_block = "10.0.0.0/16 *sesuaikan"
    cidr_block_subnet = ["10.0.1.0/24", "10.0.2.0/24" *sesuaikan]
}
```

oke konfigurasi sudah di buat semua module dan resources, selanjut nya push ke repository yang sudah di buat yang kita setting webhook nya.

buat branch baru selain branch **master,** branch **vpc-test-1.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449351104/f99a5ce0-1cec-497f-955e-67710f066aef.jpeg align="center")

nanti di repository muncul branch baru seperti di gambar. next ke **Pull Request -&gt; New Pull Request.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449370527/17d57ad1-1720-4c98-88ce-2de32c771034.jpeg align="center")

pada bagian compare pilih **vpc-test-1 & Create pull request.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449389116/09053334-294a-4590-9b5d-7da623490734.png align="center")

nanti ada proses seperti gambar di atas. jika ada error nanti seperti gambar di bawah.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449408679/a4c0ae65-87ec-41e1-af2b-a3a09a3ee6a9.jpeg align="center")

klik **details** nanti akan di arahkan ke web server atlantis.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449430630/2eb2f334-cf3f-48d5-b16e-44187b9dc2b6.jpeg align="center")

sampai sini masih dalam tahap **plan**. untuk tahap **apply** cukup comment dengan perintah **atlantis apply**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449447594/f027719e-eb74-4f2c-bd44-a54dad1993ab.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449458948/a48a052b-7452-41b6-b1a7-e8be1ad902db.jpeg align="center")

oke check di **AWS Console** apakah vpc sudah terbuat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449484777/ea8c53fb-ec50-488c-a338-ec993e3e4044.jpeg align="center")

mantapp jiwaa ....

jika sudah sesuai lakukan **merge pull request & delete branch.**

Next kita coba **Destroy.** kita buat branch baru lagi terserah nama apa **delete-vpc-test-1.** selanjut nya di file mana aja kita trigger buat comment atau apalah.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449510561/7699c473-2e63-4967-9152-7603817b5ab2.jpeg align="center")

**Commit &gt; Publish Branch.**

kembali ke github repository, ke menu **pull request &gt; new pull request.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449526745/e3e58968-5722-4097-9cd3-c69bd1148514.jpeg align="center")

**create pull request.**

comment di bawah dengan perintah **atlantis plan -- -destroy**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449551272/428071cc-e1d4-421d-996f-ed7ea294ff78.jpeg align="center")

oke ada keterangan **4 to destroy**. ydah lanjut comment **atlantis apply.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674449563383/6d889653-c759-4917-adf6-f7fa8f3b6aaa.jpeg align="center")

cek aws console dan sudah terhapus semua. jangan lupa untuk delete branch **delete-vpc-test-1.**

Lakukan **buat branch baru** jika setiap ada perubahan seperti **menambah VPC atau menambah EC2 atau yang lain2**

**Selesai ......**