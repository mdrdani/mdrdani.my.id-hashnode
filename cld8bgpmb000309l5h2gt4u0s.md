---
title: "Terraform Automation with Github & Atlantis - Session 2"
seoTitle: "Terraform Automation with Github & Atlantis - Session 2"
seoDescription: "Terraform Automation with Github & Atlantis - Session 2"
datePublished: Thu Dec 29 2022 04:35:59 GMT+0000 (Coordinated Universal Time)
cuid: cld8bgpmb000309l5h2gt4u0s
slug: terraform-automation-with-github-atlantis-session-2
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ot5kWZkH97s/upload/89f4308e68b65cb5cec88a37e897c581.jpeg
tags: aws, terraform, terragrunt

---

Lanjut dari **Session 1** sampai di sini kita akan menjalankan **web service** **atlantis.** Untuk menjalankan **web service Atlantis** kita akan membuat [Systemd Service](https://www.shubhamdipt.com/blog/how-to-create-a-systemd-service-in-linux/).

### Create Systemd Service

```bash
$ cd /etc/systemd/system
$ sudo nano atlantis.service
```

isi dengan

```bash
[Unit]
Description=service automation atlantis pull request

[Service]
ExecStart= atlantis server --atlantis-url="http://ippublicserver" --gh-user="user github" --gh-token="token personal github" --gh-webhook-secret="secret yang tadi di buat diwebhook" --repo-allowlist="url repository" --port=80 --repo-config="/home/ubuntu/atlantis-config.yaml"
Restart=always

[Install]
WantedBy=multi-user.target
```

selanjut nya di server kita bikin file **atlantis-config.yaml**﻿﻿﻿﻿﻿

```bash
$ cd /home/ubuntu
$ nano atlantis-config.yaml
```

isi dengan

```bash
repos:
- id: /.*/
 workflow: terragrunt
workflows:
 terragrunt:
  plan:
   steps:
   - env:
     name: DESTROY_PARAMETER
     command: if [ "$COMMENT_ARGS" = "\-\d\e\s\t\r\o\y" ]; then echo "-destroy"; else echo ""; fi
   - run: terragrunt plan --terragrunt-non-interactive -no-color -out=$PLANFILE $DESTROY_PARAMETER
  apply:
   steps:
   - run: terragrunt apply --terragrunt-non-interactive -no-color $PLANFILE
```

jangan lupa untuk reload service yang sudah di buat tadi

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start atlantis
$ sudo systemctl status atlantis
atlantis.service - service automation atlantis pull request
     Loaded: loaded (/etc/systemd/system/atlantis.service; disabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-12-31 10:46:42 UTC; 30s ago
   Main PID: 25724 (atlantis)
      Tasks: 7 (limit: 2309)
     Memory: 9.0M
     CGroup: /system.slice/atlantis.service
```

coba akses menggunakan ip publik server, jika terjadi **error** coba cek **security group** di AWS EC2 instance nya apakah **port 80 sudah di accept lewat publik**. Jika sudah tampilan awal seperti ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674448445018/b0975e5d-c8fa-4d18-8f30-a8ffb06f8108.jpeg align="center")

next push configuration terraform ke github.

### Configure AWS CLI

```bash
$ sudo apt install awscli
$ aws configure --profile aws-staging //proses staging
//* login aws *//
//* access key *//
/﻿/* secret key *//
```

### Create bucket storage (S3)

pergi ke **AWS Console &gt; search S3.** buat nama unik dan create bucket. AWS S3 di butuhkan untuk menyimpan state, database terraform.