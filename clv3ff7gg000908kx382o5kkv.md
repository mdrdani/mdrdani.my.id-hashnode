---
title: "Terraform Fundamental - Sesi 2 (GCP)"
datePublished: Wed Apr 17 2024 06:23:20 GMT+0000 (Coordinated Universal Time)
cuid: clv3ff7gg000908kx382o5kkv
slug: terraform-fundamental-sesi-2-gcp
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/glRqyWJgUeY/upload/7f7f716b330876e085de49abc86ae5fe.jpeg
tags: google-cloud-platform, terraform

---

kita lanjutkan ....

### Input Variables

untuk mendefinisikan value dari konfigurasi secara terpisah kita memerlukan **Input Variables.** Default dari **Variables** berupa string, namun ada jenis lainnya seperti list, map, dan boolean.

create file dengan nama **variables.tf**

```bash
variable "compute_zone" {
  type    = string
  default = "asia-southeast2-a"
}
```

pada file **main.tf** di rubah pada bagian **Zone**

```bash
resource "google_compute_instance" "VM-Satu" {
  name                      = "vm-satu"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone
  allow_stopping_for_update = true

......
}
```

Selain variables berupa string,kita akan coba juga menggunakan variable berupa **list** ini mirip dengan array. Value pada **list** variable memiliki index dan index di mulai dari angka 0.

Contoh di sini jika kita ingin mengumpulkan beberapa zone yang ada di GCP.

pada file **variables.tf** kita bisa rubah seperti ini.

```bash
variable "compute_zone" {
  type = list(any)
  default = [
    "asia-southeast2-a",
    "asia-southeast2-b",
  "asia-southeast2-c"]
}
```

dan digunakan di **main.tf** akan seperti ini,

```bash
resource "google_compute_instance" "VM-Satu" {
  name                      = "vm-satu"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone[0]
  allow_stopping_for_update = true

.....
}
```

Selain variables berupa list,kita akan coba juga menggunakan variable berupa **map** yang merupakan collection dari key dan string. untuk memanggil valuenya, kita dapat menggunakan key yang ada.

masih menggunakan contoh zone pada bagian **variables.tf** di rubah menjadi seperti ini

```bash
variable "compute_zone" {
  type = map(any)
  default = {
    "zone-1" = "asia-southeast2-a"
    "zone-2" = "asia-southeast2-b"
    "zone-3" = "asia-southeast2-c"
  }
}
```

pada bagian **main.tf** kita menggunakan nya seperti ini

```bash
resource "google_compute_instance" "VM-Satu" {
  name                      = "vm-satu"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone["zone-1"]
  allow_stopping_for_update = true

.....
}
```

Selain variables berupa map,kita akan coba juga menggunakan variable berupa **boolean** yang kita definisikan true/false value.

contoh di sini pada file **variables.tf**

```bash
variable "allow_stop_vm" {
  type    = bool
  default = false
}
```

dan cara menggunakan nya di **main.tf** seperti ini

```bash
resource "google_compute_instance" "VM-Satu" {
  name                      = "vm-satu"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone["zone-1"]
  allow_stopping_for_update = var.allow_stop_vm

.....
}
```

> Tips: untuk melakukan override value variable pada saat **terraform plan & apply** , kita dapat passing options -var
> 
> ex: terraform plan -var allow\_stop\_vm=false

Jika ingin memasukan **ssh-key** langsung ke instance saat instance tersebut di create pertama kali kita bisa masukan **ssh-key** ke file **variables.tf**

contoh di sini pada file **variables.tf**

```bash
variable "ssh_keys" {
  type    = string
  default = "muhamaddani3004:ssh-rsa fdsfsf....."
}
```

dan cara menggunakan nya di **main.tf** seperti ini

```bash
resource "google_compute_instance" "VM-Satu" {
  name                      = "vm-satu"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone["zone-1"]
  allow_stopping_for_update = var.allow_stop_vm
  
  metadata = {
    ssh-keys = var.ssh_keys
  }
.....
}
```

jika nanti kita akan lakukan **ssh** kita lgsung sudah bisa masuk ke instance.

### Output Value

kita dapat menampilkan suatu argument dari sebuah Resource dari command line, kurang lebih contoh seperti menampilkan VM kita dapat IP Publik berapa agar kita tidak perlu lagi check console.

kita tempatkan output value di file **outputs.tf**

```bash
output "public_ip_vm_satu" {
  value       = google_compute_instance.VM-Satu.network_interface[0].access_config[0].nat_ip
  description = "IP Public VM Satu"
}

output "public_ip_vm_dua" {
  value       = google_compute_instance.VM-Dua.network_interface[0].access_config[0].nat_ip
  description = "IP Public VM Dua"
}
```

dan hasil output nya di dapat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713327826692/0e244aba-34f1-4752-9d75-23a5b18ed1ae.png align="center")

atau bisa dengan command

```bash
$ terraform output public_ip_vm_dua
$ terraform output public_ip_vm_satu
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713327895924/92ab76b8-4fba-4e8e-ad2c-f9ef9e269472.png align="center")

### Backend

Sebelumnya kita sudah bahas apa itu [State File](https://developer.hashicorp.com/terraform/language/state). file tersebut secara default di letakan di folder konfigurasi Terraform dengan nama **terraform.tfstate.**

Dalam best practice file tersebut tidak di push ke VCS seperti git. Namun repot kalau jika file state tersebut hilang ya salah satunya broken deployment.

Pada umumnya kita letakan file tersebut di Object Storage seperti Google Cloud Storage atau S3 AWS.

Best practicenya kita tulis konfigurasi backend di file **backend.tf**

```bash
terraform {
  backend "gcs" {
    bucket = "mdrdani-tfstate"
  }
}
```

sebelumnya kita create terlbih dahulu Object Storage nya, masuk console Google Cloud.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713329086106/926662e6-539c-4aed-906b-0ee5dff12fff.png align="center")

karena konfigurasi **Backend** ini letaknya di \*\*Terraform Blocks,\*\*maka kita harus menjalankan ulang perintah **terraform init.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713329246004/44329e11-386d-4dd6-824d-180e8ab592c0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713329280150/57630540-fa6d-444f-a13a-073bde3556ba.png align="center")

### Terraform State Command

State Command digunakan untuk melakukan manajemen state yang suda ada,yang umumnya di gunakan untuk melihat list, detail atau menghapus state resource.

```bash
$ terraform state list

$ terraform state show {state_name}

$ terraform state rm {state_name}
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713329488325/10069136-05a1-4acd-837b-6d934047f993.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713329819042/9050327e-07ca-4713-879e-e2f3aa0ef00c.png align="center")

### Terraform Destroy Command

jika resource tidak terpakai lagi seharusnya kan kita hapus agar tidak menambah beban cost yang dikeluarkan. Untuk menghapus resource dapat menggunakan destroy command.

```bash
//menghapus spesifik resource
$ terraform destroy -target <type>.<name>

//menghapus semua resource
$ terraform destroy
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713330007182/7385c23e-dce2-49c2-9896-a048ce975ec9.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713330137915/37c7a1da-e092-45e1-a127-3117a85817f2.png align="center")

> Tips: sebelumnya kita dapat menghapus secara spesifik resource mana yang ingin di hapus, ini juga berlaku untuk terraform plan & apply

```bash
//plan spesifik resource
$ terraform plan -target <type>.<name>

// apply spesifik resource
$ terraform apply -target <type>.<name>
```

### Resource Lifecycle

Digunakan untuk mengontrol behavior dari sebuah resource saat dibuat,di update atau dihapus. Contoh kita bisa mencegah suatu resource terhapus secara tidak sengaja.

```bash
resource "google_compute_instance" "VM-Dua" {
  name                      = "vm-dua"
  machine_type              = "e2-micro"
  zone                      = var.compute_zone["zone-1"]
  allow_stopping_for_update = var.allow_stop_vm

 lifecycle {
    prevent_destroy = true
  }
  ....
}
```

Oke thanks yang sudah membaca materi nya.