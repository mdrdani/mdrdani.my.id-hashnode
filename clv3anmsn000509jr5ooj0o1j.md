---
title: "Terraform Fundamental - Sesi 1 (GCP)"
datePublished: Wed Apr 17 2024 04:09:55 GMT+0000 (Coordinated Universal Time)
cuid: clv3anmsn000509jr5ooj0o1j
slug: terraform-fundamental-sesi-1-gcp
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Fa9b57hffnM/upload/a5790e2c4165d49d27d0895d8e79453c.jpeg
tags: google-cloud-platform, terraform

---

Pada kalangan DevOps / SRE tidak asing lagi dengan tools yang satu ini Terraform merupakan tools Infrastructure as Code untuk mempercepat dalam mengelola cloud seperti AWS, GCP, atau Azure namun tidak hanya cloud kita juga dapat menggunakan terraform untuk memanage Virtualisasi seperti Proxmox ataupun Container Docker. Terraform adalah tool open source yang dibuat oleh Hashicorp. Dengan terraform kita dapat membuat, mengubah, menghapus dan menduplikasikan infrastructure.

### **Core Terraform Workflow**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393759006/c98f1aa3-d68a-4870-9f3d-5fea8368c03d.avif align="center")

**Write** : menulis terraform configuration dan initialize

mendefinisikan infrastructure pada file konfigurasi

**Plan :** Preview

Sebelum dieksekusi ke infrastuk kita bisa melakukan preview terlebih dahulu apa yang akan di buat, ubah atau hapus sehingga bisa lebih aman saat di eksekusi ke infrastuktur.

**Apply :** Execution

Setelah tahap plan (preview) maka kita bisa eksekusi ke infrastucture yang sebenarnya.

### **Terraform Command**

cukup banyak command yang dimiliki oleh terraform yang bisa digunakan, namun untuk awal kita hanya cukup mengetahui beberapa command yang biasa di pakai. Berikut ini beberapa command dasar :

* *terraform init =&gt; inisialisasi*
    
* *terraform fmt=&gt; merapihkan konfigurasi code file*
    
* *terraform validate =&gt; check apakah konfigurasi code sudah sesuai*
    
* *terraform plan =&gt; check apa saja yang akan di rubah*
    
* *terraform apply =&gt; menerapkan perubahan*
    
* *terraform destroy=&gt; menghapus*
    

### **Install Terraform**

Download Terafform di situs ini [**https://developer.hashicorp.com/terraform/downloads**](https://developer.hashicorp.com/terraform/downloads). Pilih operating system menggunakan apa, di sini saya menggunakan windows.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393925368/5e29778c-76b2-465e-86b7-e977fdc404f0.jpeg?auto=compress,format&format=webp align="left")

Buka file zip nya copy **terraform.exe** ke drive C: dengan membuat folder terraform.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393944477/066779bd-3180-47b7-98ff-2878d6d8dd8c.jpeg?auto=compress,format&format=webp align="left")

Update system global path pada windows di bagian **Control Panel -&gt; System -&gt; System Settings-&gt; Environment Variable**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393965767/aed79b41-12bb-4110-b4c5-760a01e54925.jpeg?auto=compress,format&format=webp align="left")

**Oke Save,** dan test buka CMD kembali.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1674393994650/dd114a92-48d0-4be6-a132-340d548f5c65.jpeg?auto=compress,format&format=webp align="left")

Sukses terraform sudah terinstall di windows.

### Setup gcloud CLI

Rekomendasi jika menggunakan PC/Laptop pribadi adalah dengan menggunakan **gcloud.** Kita akan menggunakan **Application Default Credentials (ADC)** dari google. install dahulu gcloud cli dari link ini [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713192670055/a4a424eb-1f49-49d3-a0d2-0edf00f3396e.png align="center")

biasa nya instalasi memakan waktu sekitar 5 menitan.

```bash
# gcloud auth login
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713193612315/7f81c637-9461-4dc9-9da2-203ab8addb2f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713193663026/c07d87fe-2e74-42cc-a7e1-344ad1982e05.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713193691025/36d21ed7-60f7-48ec-8757-c99f234fed94.png align="center")

sukses authentikasi menggunakan account google cloud. selanjutnya login ADC agar terraform dapat berkomunikasi dengan Google Cloud.

```bash
# gcloud auth application-default login
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713194335434/1a68e8b6-57c6-4857-b66a-ba892255ecb2.png align="center")

### Terraform Provider

Terraform provider digunakan sebagai penjembatan antara Terraform dengan remote system (Cloud / On-Premise). Didalam [Terraform Block](https://developer.hashicorp.com/terraform/language/settings) kita harus menentukan provider yang akan digunakan. Minimal kita definisikan source & version yang kita gunakan.

Dalam Visual studio code, create file dengan nama **main.tf**

```bash
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "5.24.0"
    }
  }
}
```

setelah itu pada bagian provider block kita dapat mendefinisikan konfigurasi dari provider namun ini optional di biarkan blank juga tidak apa, contoh

```bash
...
provider "google" {
  # Configuration options
  project = "development-m-dani"
  region = "asia-southeast2" //jakarta
}
```

> *tips :*
> 
> * *jika ingin merapihkan reformat konfigurasi file main.tf cukup ketikan perintah****terraform fmt.***
>     
> * jika ingin melakukan pengecekan konfigurasi dapat memvalidasi menggunakan perintah **terraform validate.**
>     
> * jika pertama kali mendefinisikan Terraform Block atau ada perubahan gunakan perintah **terraform init.**
>     

### Resources Blocks

Resource blocks merupakan tempat kita mendefinisikan object dari infrastruktur. Contoh kita akan membuat VM atau istilah nya di GCP yaitu Google Compute Instance. [Dokumentasi](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/compute_instance).

Tambahkan di **main.tf**

```bash
...
resource "google_service_account" "my_service_account" {
  account_id   = "mdrdani-sa"
  display_name = "mdrdani Service Account"
}

resource "google_compute_firewall" "http" {
  name    = "allow-http"
  network = "default"

  allow {
    protocol = "tcp"
    ports    = ["80"]
  }

  source_ranges = ["0.0.0.0/0"]
}

resource "google_compute_firewall" "https" {
  name    = "allow-https"
  network = "default"

  allow {
    protocol = "tcp"
    ports    = ["443"]
  }

  source_ranges = ["0.0.0.0/0"]
}

resource "google_compute_instance" "VM-Satu" {
  name         = "vm-satu"
  machine_type = "e2-micro"
  zone         = "asia-southeast2-a" //jakarta
  tags         = ["http-server", "https-server"]

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }

  network_interface {
    network = "default"

    access_config {
      // Ephemeral public IP
    }
  }

  #   metadata_startup_script = "echo hi > /test.txt"

  service_account {
    # Google recommends custom service accounts that have cloud-platform scope and permissions granted via IAM Roles.
    # email  = google_service_account.default.email
    scopes = ["cloud-platform"]
  }
}
```

jika sudah kita coba ketikan perintah **terrafom plan**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713199694457/93557fd9-4ace-40da-802d-adc184bde22b.png align="center")

terlihat di bagian **Plan** ada 1 to add yang artinya kita akan membuat 1 VM. Jika sudah sesuai maka selanjutnya kita ketikan perintah **terraform apply.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713199873340/c7184eb8-75af-4af6-8590-92c6af3e272f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713199937726/71ce61c8-4df0-4596-9398-5f5b1398df46.png align="center")

**apply complete !!** kita sudah sukses create VM Google CLoud lewat Terraform.

> tips: jika kita auto melewati pertanyaan yes, maka bisa menggunakan perintah **terraform apply -auto-approve**

### Data Sources

Untuk mendefinisikan informasi yang nanti nya dapat dipanggil dari Blocks lain kita gunakan **Data Sources**, biasa nya berdampingan dengan **Resources Blocks.** Contoh di sini kita definisikan image booting untuk VM. [Dokumentasi](https://registry.terraform.io/providers/hashicorp/google/latest/docs/data-sources/compute_image)

tambahkan di **main.tf**

```bash
....

data "google_compute_image" "my_image" {
  family  = "debian-11"
  project = "debian-cloud"
}

resource "google_compute_instance" "VM-Satu" {
  .....

  boot_disk {
    initialize_params {
      image = data.google_compute_image.my_image.self_link
    }
  }

  .....
  }

...
```

di sini ada kata **self\_link** yang artinya merupakan link referensi unik suatu resource.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713280901921/38f2523b-d22e-4575-af95-06822ad6398f.png align="center")

> Tips: untuk mendapatkan list public image Google Cloud kita bisa mengetikan perintah **gcloud compute images list**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713281628070/ec354125-39ce-4b25-9234-087a3ecdf297.png align="center")

### Accessing Resource Attributes

kita terkadang akan membutuhkan memanggil sebuah attribute suatu resource dari tempat lain, untuk memanggil nya kita dapat menggunakan format **&lt;TYPE&gt;.&lt;NAME&gt;.&lt;ATTRIBUTE&gt;.**

[**Dokumentasi**](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/google_service_account)**1**

[**Dokumentasi2**](https://cloud.google.com/compute/docs/access/service-accounts)

contoh kita akan membuat **Service Account GCP** dan menggunakan nya di VM yang telah kita buat.

tambahkan pada bagian **main.tf**

```bash
....

resource "google_service_account" "service_account" {
  account_id   = "service-account-id"
  display_name = "Service Account"
}

resource "google_compute_instance" "VM-Satu" {
....
service_account {
    # Google recommends custom service accounts that have cloud-platform scope and permissions granted via IAM Roles.
    email  = google_service_account.my_service_account.email
    scopes = ["cloud-platform"]
  }
....
}
...
```

jika terjadi error VM tidak dapat restart pada saat update **Service Account** kita dapat menambahkan **allow\_stopping\_for\_update = true**

```bash
....

resource "google_compute_instance" "VM-Satu" {
    name                      = "vm-dua"
    machine_type              = "e2-micro"
    zone                      = "asia-southeast2-a" //jakarta
    allow_stopping_for_update = true
....
}

...
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713283547688/254db535-2174-4bca-85c1-eb940817d6d4.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713283712999/f8a949b6-796b-477a-84e7-a08e86a68fb9.png align="center")