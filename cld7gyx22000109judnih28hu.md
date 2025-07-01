---
title: "Terraform Fundamental - Sesi 2 (AWS)"
seoTitle: "Terraform Fundamental - Sesi 2 (AWS)"
seoDescription: "Terraform Fundamental - Sesi 2 (AWS)"
datePublished: Tue Dec 20 2022 14:22:47 GMT+0000 (Coordinated Universal Time)
cuid: cld7gyx22000109judnih28hu
slug: terraform-practice-session-2-aws
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/aWslrFhs1w4/upload/1e2b476ee92228e64b6f133fd6ebf24d.jpeg
tags: aws, terraform

---

Next di Part 2 kita akan ulik lebih lanjut lagi tidak hanya create EC2 saja, namun kita akan membuat seperti VPC, Security group dll.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397122528/96ca1388-a368-4d6f-96c1-74ca0cd8f1ea.webp align="center")

Hal-hal yang perlu disiapkan untuk ngelab adalah sebagai berikut :

* Install Terraform
    
* Code editor : VScode
    
* Extensions VScode : Terraform, Hashicorp Terraform
    
* AWS Account
    

Struktur folder nya akan seperti ini nanti nya.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397137242/af494fed-ee95-49dd-a7b0-065979e3e980.jpeg align="center")

**Create VPC**

buat file dengan nama [**VPC.tf**](http://VPC.tf)**,** dan isi seperti ini.

```bash
resource "aws_vpc" "vpc-node1" {
  cidr_block       = "10.0.0.0/16"


  tags = {
    Name = "vpc-node"
  }
}
```

selanjutnya seperti biasa kita check dahulu

```bash
$ terraform plan
```

jika sudah sesuai langsung kita apply

```bash
$ terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397167931/f433e01d-1ecc-4f7d-9a22-d87a01231b75.jpeg align="center")

**Create Subnet**

buat file dengan nama [**subnet.tf**](http://subnet.tf)**,** dan isi seperti ini.

```bash
resource "aws_subnet" "subnet-node1" {
  vpc_id     = aws_vpc.vpc-node1.id
  cidr_block = "10.0.1.0/24"


  tags = {
    Name = "subnet-node1"
  }
}


resource "aws_subnet" "subnet-node2" {
  vpc_id     = aws_vpc.vpc-node1.id
  cidr_block = "10.0.2.0/24"


  tags = {
    Name = "subnet-node2"
  }
}
```

kita membuat 2 subnet dan cidr\_block yang berbeda. dan selanjut nya check dengan command

```bash
$ terraform plan
```

next apply

```bash
$ terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397185310/2c23571d-e237-43b2-a6f1-a123f4c09565.jpeg align="center")

**Create Private VM EC2**

kembali ke file [**ec2.tf**](http://ec2.tf) tadi, edit seperti ini

```bash
resource "aws_instance" "VM-node1" {
  ami           = "ami-0a2bcdce90f0df342"
  instance_type = "t3.micro"


  tags = {
    Name = "VM-node1"
  }


  subnet_id = aws_subnet.subnet-node1.id
}


resource "aws_instance" "VM-node2" {
  ami           = "ami-0a2bcdce90f0df342"
  instance_type = "t3.micro"


  tags = {
    Name = "VM-node2"
  }


  subnet_id = aws_subnet.subnet-node2.id
}


```

Cheking plan

```bash
$ terraform plan
```

dan apply

```bash
$ terraform apply
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397216966/b778abb2-7d81-4de1-b3c3-a82708cb61df.jpeg align="center")

ok sukses masuk ke subnet 1.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674397229633/9206d102-37e9-4e3b-8843-a5e0113be087.jpeg align="center")

oke sukses masuk ke subnet 2.

### **Create Internet Gateway**

buat file dengan nama [**internet-gateway.tf**](http://internet-gateway.tf), dan isi seperti ini

```bash
resource "aws_internet_gateway" "internet-gw-node" {
  vpc_id = aws_vpc.vpc-node1.id

  tags = {
    Name = "internet-gw-node"
  }
}
```

checking plan

```bash
$ terraform plan
```

dan apply

```bash
$ terraform apply
```

### **Create Route Table**

buat file dengan nama [**route-table.tf**](http://route-table.tf), dan isi seperti ini

```bash
resource "aws_route_table" "route-table-node" {
  vpc_id = aws_vpc.vpc-node1.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.internet-gw-node.id
  }

  tags = {
    Name = "route-table-node"
  }
}
```

checking plan

```bash
$ terraform plan
```

dan apply

```bash
$ terraform apply
```

### **Create Security Group**

buat file dengan nama [**security-group.tf**](http://security-group.tf), dan isi seperti ini

```bash
resource "aws_security_group" "security-group-node" {
  name        = "security-group-node"
  description = "Allow  inbound traffic"
  vpc_id      = aws_vpc.vpc-node1.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "security-group-node"
  }
}
```

checking plan

```bash
$ terraform plan
```

dan apply

```bash
$ terraform apply

```

agar VM EC2 di subnet1 dapat di akses dari luar Masuk ke bagian **Subnets** di **VPC ke Tab Route Table** edit **route.**

### **Create Key Pair**

untuk SSH ke VM instance maka kita perlu mengenerate SSH key pair. Pada Power shell cukup ketikan

```bash
$ ssh-keygen -C ubuntu
```

nanti otomatis akan membuat kan SSH key pair.

```bash
resource "aws_key_pair" "key-pair-node" {
  key_name   = "node-key"
  public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD3F6tyPEFEzV0LX3X8BsXdMsQz1x2cEikKDEY0aIj41qgxMCP/iteneqXSIFZBp5vizPvaoIR3Um9xK7PGoW8giupGn+EPuxIA4cDM4vzOqOkiMPhz5XK0whEjkVzTo4+S0puvDZuwIsdiW9mxhJc7tgBNL0cYlWSYVkz4G/fslNfRPW5mYAM49f4fhtxPb5ok4Q2Lg9dPKVHO/Bgeu5woMc7RY0p1ej6D4CKFE6lymSDJpW0YHX/wqE9+cfEauh7xZcG0q9t2ta6F6fmX0agvpFyZo8aFbXeUBr7osSCJNgvavWbM/06niWrOvYX2xwWdhXmXSrbX8ZbabVohBK41 email@example.com"
}
```

checking plan

```bash
$ terraform plan
```

dan apply

```bash
$ terraform apply
```

Jika sudah memasukan key pair, maka selanjut nya kita coba untuk remote salah satu VM EC2. Remote VM EC 2 di subnet 1 saja, kita edit file [ec2.tf](http://ec2.tf) nya menjadi seperti ini.

```bash
. . . . . 
  
    subnet_id                   = aws_subnet.subnet-node1.id
      associate_public_ip_address = true
      vpc_security_group_ids      = [aws_security_group.security-group-node.id]
      key_name                    = aws_key_pair.key-pair-node.key_name
    }
```

checking plan

```bash
$ terraform plan
```

dan apply, pada tahap ini karena kita akan merubah key pair pada salah satu instance jadi akan di destroy dahulu selanjutnya akan di buatkan sama seperti awal.

```bash
$ terraform apply
```

instance 1 sudah ada public IP nya. selanjut nya kita akan SSH ke instance tersebut.

```bash
$  ssh -i key-pair-node ubuntu@IP-PUBLIK-INSTANCE
```

Jika semua yang di coba sudah selesai dan tidak ingin digunakan kembali, kita dengan mudah menghapus semua VM, VPC, Subnet dll dengan command

```bash
$ terraform destroy
```

Jika ingin destroy secara spesifik dapat menggunakan

```bash
$ terraform destroy --target="nama resource.nama label"
$ terraform destroy --target="aws_instance.node3"﻿
```

dan jika ingin memulai lagi semua tinggal command lagi

```bash
$ terraform apply
```