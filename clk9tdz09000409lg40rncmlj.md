---
title: "Create Server on AWS EC2 Instance with Terraform"
datePublished: Wed Jul 19 2023 14:24:09 GMT+0000 (Coordinated Universal Time)
cuid: clk9tdz09000409lg40rncmlj
slug: create-server-on-aws-ec2-instance-with-terraform
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Im_cQ6hQo10/upload/286a919a92c42636dca6e0c77b259b1a.jpeg
tags: aws, terraform

---

![](https://file.notion.so/f/s/7265d55c-b9b6-49db-83c7-82607319702f/Untitled.png?id=d7ec400e-2574-4ccf-b45f-e01e9ed987c4&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=eX6UnYOyjD7-jmqcs7z91NP11JlF6B42hhtD1Pvseaw&downloadName=Untitled.png align="center")

**module/main.tf**

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region     = var.region
  access_key = var.access_key
  secret_key = var.secret_key
}

resource "aws_instance" "default" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name = var.name
  }

  associate_public_ip_address = var.public_ip
  subnet_id                   = var.subnet_id
  key_name                    = var.key_name
  vpc_security_group_ids      = var.vpc_security_group_ids
}
```

**Module/Output.tf**

```bash
output "instance_id" {
  value = aws_instance.default.id
}

output "public_ip" {
  value = aws_instance.default.public_ip
}
```

**Module/Vars.tf**

```bash
variable "region" {
  type    = string
  default = "ap-southeast-1" //Region Jakarta
}

variable "access_key" {
  type    = string
  default = "AKIA4KA*********" //buat User di IAM AWS
}

variable "secret_key" {
  type    = string
  default = "6klZ95kRV+OZWC**********" //buat User di IAM AWS
}

variable "ami" {
  type    = string
  default = "ami-082b1f4237bd816a1" //lihat AMI ID saat create instance EC2 AWS
}

variable "instance_type" {
  type    = string
  default = "t2.medium" //lihat Instance Type saat create instance EC2 AWS
}

variable "name" {
  type = string
}

variable "public_ip" {
  type    = bool
  default = true
}

variable "subnet_id" {
  type    = string
  default = "subnet-0af42ddc7c8b1182f" //lihat di VPC AWS bagian subnet group
}

variable "key_name" {
  type    = string
  default = "****" //create Key di bagian IAM AWS
}

variable "vpc_security_group_ids" {
  type    = list(any)
  default = ["sg-03527e3d13cc3706c"] //lihat di bagian VPC AWS
}
```

[**server.tf**](http://server.tf)

```bash
module "Jenkins-server" {
  source = "./module"
  name   = "Jenkins-server"
}

output "Jenkins_server_id" {
  value = module.Jenkins-server.instance_id
}

output "Jenkins_server_public_ip" {
  value = module.Jenkins-server.public_ip
}
```

```bash
$ terraform init
$ terraform plan
$ terraform apply
```

![](https://file.notion.so/f/s/83725690-69ba-4405-8487-9b1cf95ac659/Untitled.png?id=a5cf0f47-1065-4717-995c-a3e9683a7e6b&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=rEzx7Pc2ULzw2DOFJN5LmjXTIAPjtC0-dWSKmsThSK0&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/17fb7b44-026b-4f3c-9212-cdd5eca7f44e/Untitled.png?id=269d0695-ff95-4171-a5d5-2166c3044883&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=9ubtbF28fvy8NjevaM3Yz8StrSek5KOBOPYZwDUNGo4&downloadName=Untitled.png align="left")