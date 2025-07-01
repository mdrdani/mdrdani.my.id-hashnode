---
title: "Ansible Practice - Session 1"
seoTitle: "Ansible Practice - Session 1"
seoDescription: "Ansible Practice - Session 1"
datePublished: Fri Jan 13 2023 05:35:44 GMT+0000 (Coordinated Universal Time)
cuid: cld8cy3fo000409lfhys99cd4
slug: ansible-practice-session-1
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/slbqShqAhEo/upload/eade33c6dee324aa11b8693a019c0610.jpeg
tags: ansible, terraform

---

Gaess kemarin di materi awal kita sudah install ansible dan sedikit penjelasan nya kenapa tools software ini banyak di pakai di lingkungan DevOps. Sekarang ini kita mencoba untuk studi kasus biar kita real yakan gimana cara penggunaan nya. **Jadi cerita nya kita punya website landing page yang menggunakan database mysql, dan website akan di bagi ke 2 server menggunakan load balancer nginx.**

**Requirement**

* Ansible
    
* Terraform
    
* Akun AWS
    

### Create VM EC2 AWS

kita buat VM menggunakan terraform ya, siapin file konfigurasi nya. Inget semua file konfig ini copy dari [Terraform Doc](https://registry.terraform.io/providers/hashicorp/aws/latest/docs).

struktur folder :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674450791190/2630ffdc-f5f7-4271-87f2-4c7f6e8b3066.png align="center")

[**main.tf**](http://main.tf)

```bash
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "4.48.0"
    }
  }
}

provider "aws" {
  region = var.region
  access_key = var.access_key
  secret_key = var.secret_key
}

resource "aws_instance" "default" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name = var.name
  }

  associate_public_ip_address = var.public_ip
  subnet_id = var.subnet_id
  key_name = var.key_name
  vpc_security_group_ids = var.vpc_security_group_ids
}
```

[**output.tf**](http://output.tf)

```bash
output "instance_id" {
  value = aws_instance.default.id
}

output "public_ip" {
  value = aws_instance.default.public_ip
}
```

[**vars.tf**](http://vars.tf)

```bash
variable "region" {
  type = string
  default = "ap-southeast-3" //Region Jakarta
}

variable "access_key" {
  type = string
  default = "***" //buat User di IAM AWS
}

variable "secret_key" {
  type = string
  default = "****" //buat User di IAM AWS
}

variable "ami" {
  type = string
  default = "ami-0796a4cfd3b7bec87" //lihat AMI ID saat create instance EC2 AWS
}

variable "instance_type" {
  type = string
  default = "t3.micro" //lihat Instance Type saat create instance EC2 AWS
}

variable "name" {
  type = string
}

variable "public_ip" {
  type = bool
  default = true
}

variable "subnet_id" {
  type = string
  default = "subnet-043160dc0c75a5d57" //lihat di VPC AWS bagian subnet group
}

variable "key_name" {
  type = string
  default = "***" //create Key di bagian IAM AWS
}

variable "vpc_security_group_ids" {
  type = list
  default = ["sg-03dab3277a1b07861"] //lihat di bagian VPC AWS
}
```

jika sudah buat module terraform nya, next kita akan buat server nya.

struktur folder:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674450844327/5246cc29-746d-4aaa-9695-37e1d4225af0.png align="center")

[**landing-page-1.tf**](http://landing-page-1.tf)

```bash
module "landing-page-1"{
    source = "../Terraform/terraform-module/module/ec2" //arahkan source ke module ec2
    name = "landing-page-1"
}

output "landing_page_1_public_ip" {
    value = module.landing-page-1.public_ip
}
```

[**landing-page-2.tf**](http://landing-page-2.tf)

```bash
module "landing-page-2"{
    source = "../Terraform/terraform-module/module/ec2" //arahkan source ke module ec2
    name = "landing-page-2"
}

output "landing_page_2_public_ip" {
    value = module.landing-page-2.public_ip
}
```

[**mariadb-server.tf**](http://mariadb-server.tf)

```bash
module "mariadb-server"{
    source = ".../Terraform/terraform-module/module/ec2" //arahkan source ke module ec2
    name = "mariadb-server"
}

output "mariadb_server_public_ip" {
    value = module.mariadb-server.public_ip
}
```

[**load-balancer.tf**](http://load-balancer.tf)

```bash
module "load-balancer"{
    source = "../Terraform/terraform-module/module/ec2" //arahkan source ke module ec2
    name = "load-balancer"
}

output "load_balancer_public_ip" {
    value = module.load-balancer.public_ip
}
```

dan jalankan command.

```bash
~/server$ terraform init
~/server$ terraform plan
~/server$ terraform apply
```

### Create Config Ansible

Semua konfigurasi dapat di lihat di sini [Ansible Builtin](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/), atau dengan keyword di google jika contoh ingin **apt update** tinggal ketik saja **ansible apt.**

struktur folder :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674450929208/c5697625-a988-41c1-9c80-a15e3a20aabc.png align="center")

**inventory**

```bash
[landing-page]
108.137.44.176
108.137.123.14
﻿
[load-balancer]
108.136.230.0 

[database]
108.137.62.183

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=.../***.pem //private key extensi .pem di dapat saat create IAM User
```

### Create Roles Update-OS

next kita bikin roles update OS, buat folder di folder **roles** dengan nama **update-os,** dan di dalam folder **update-os** create folder **tasks.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675759627392/01d774f4-3ae0-4bcd-962b-38316cbe50e2.png align="center")

**main.yaml**

```yaml
- name: update server linux Debian/Ubuntu
  shell: apt update
  when: ansible_distribution == 'Debian' or ansible_distribution == 'Ubuntu'


- name: update server linux redhat/centos
  shell: apt update
  when: ansible_distribution == 'CentOs' or ansible_distribution == 'Red Hat Enterprise Linux'
```

### Create Roles Nginx

next kita bikin roles install Nginx, buat folder di folder **roles** dengan nama **Nginx,** dan di dalam folder **Nginx** create folder **tasks.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675759651960/f9b3ecdc-37cb-4ea8-ac9d-73e2a2e100af.png align="center")

**main.yaml**

```yaml
- name: install service nginx
  apt:
    name: nginx
    state: present
```

### Create Playbook Landing Page

**landing-page-playbook.yaml**

```yaml
- name: setup landing page server
  hosts: landing-page
  become: yes
  roles:
    - roles/update-os
    - roles/nginx
```

dan jalankan command

```bash
~/studi-kasus$ export ANSIBLE_HOST_KEY_CHECKING=false
~/studi-kasus$ ansible-playbook -i inventory landing-page-playbook.yaml
```

cek apakah di server sudah terupdate OS dan sudah terlihat web server Welcome to Nginx.