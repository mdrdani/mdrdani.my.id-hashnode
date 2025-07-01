---
title: "Ansible Fundamental - Sesi 1"
datePublished: Wed Jan 11 2023 17:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clvbte2iq000i09jre4gf18k1
slug: ansible-fundamental-sesi-1
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/t4qI2IDcL5s/upload/78feae2bbde649094b4c72bfc8c21f97.jpeg
tags: linux, ansible, google-cloud-platform

---

### Pengenalan Ansible

Berangkat dari permasalahan kenapa Ansible di ciptakan dan kita butuh memakai nya? Ketika kita konfigurasi server Linux maka kita akan melakukan remote SSH lalu melakukan task yang ingin kita konfigurasi.

Bagaimana kalau server linux yang kita miliki berjumlah puluhan,dengan konfigurasi yang sama? ada beberapa permasalahan yang mungkin kita temui.

* Capek / Pegel
    
* Ada perintah command yang terlewat
    
* Perintah yang di jalankan tidak terdokumentasi dengan baik.
    

Ansible merupakan tools untuk mengotomatisasi tugas-tugas seperti membuat dan mengkonfigurasi resource linux, windows, database dll.

![What is Ansible and What Does it Do? | Liquid Web](https://res.cloudinary.com/lwgatsby/f_auto/www/uploads/2023/05/image01-How-Ansible-Works-Rich-Media-min.jpg align="left")

Kelebihan ansible adalah sifatnya *agent-less,* maksudnya itu tidak perlu menginsal *agent* di setiap server yang ingin di otomasti. cukup install ansible di laptop/komputer admin saja.

Cara kerja Ansible yaitu tugas tugas yang akan di jalankan dibungkus dalam sebuah **module,** setiap **module** mempunyai tugas kecil secara spesifik seperti **copy file, install web server,** atau **create user.**

Di Ansible juga ada nama nya konsep **Inventory,** yang dimana kita dapat mendefinisikan IP/Domain dalam satu file yang nanti nya Ansible akan membaca file **Inventory** tersebut sebelum membaca **module.**

### Menginstall Ansible

Ansible berjalan diatas bahasa pemrograman Python. Khusus sistem operasi windows, Ansible menyarankan menggunakan [WSL](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#control-node-requirements).

Untuk menginstall Ansible cukup ikuti dokumentasi [ini](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pip).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713540977860/c6edb346-7707-4e71-b72f-4aa31a869946.png align="center")

### Prepare Linux Server

untuk Server kita coba create melalui **Terraform** di Google Cloud Platform.

Jika sudah terbuat selanjutnya lakukan **SSH** ke server yang sudah dibuat.

Check apakah server sudah terinstall **Python**

```bash
$ python3 --version
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713627856386/5e6f8c93-6167-4c98-bf90-9c44a97303c3.png align="center")

### Ansible Inventory

Seperti yang sudah di bahas sebelumnya ansible mengetahui server mana yang akan kita manage melalui file [**Inventory**](https://docs.ansible.com/ansible/latest/plugins/inventory.html#enabling-inventory-plugins)**.**

Ansible mendukung banyak sekali format penulisan file **Inventory,** yaitu format **INI** dan **YAML.**

Selanjutnya kita coba buat file dengan nama \*\*Inventory,\*\*dan isikan IP Public dari server yang akan di manage

**inventory**

```bash
//ip server dan private key
34.128.82.28 ansible_user=muhamaddani3004 ansible_ssh_private_key_file=./ssh-key/cehamot
```

lalu kita coba ping untuk memastikan koneksi ke server.

```bash
$ ansible 34.128.82.28 -i ./inventory -m ping
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713631552544/1a6de9f7-36de-4cb2-b68f-5e004f48731a.png align="center")

response yang di dapat adalah **Success**,berarti kita telah berhasil terhubung ke server melalui **ansible.**

Biasanya satu file inventory memiliki list ip/domain server, agar file inventory terlihat rapih kita dapat kelompokan dalam sebuah [**group**](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#inventory-basics-formats-hosts-and-groups)**.**

Bahkan parameter yang sudah kita buat sebelumnya "sangat tidak clean" didefinisikan berulang-ulang, bagaimana kalau kita buat [**group variables**](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#assigning-a-variable-to-many-machines-group-variables)**.**

```bash
[server_test]
34.128.82.28

[server_test:vars]
ansible_user=muhamaddani3004
ansible_ssh_private_key_file=./ssh-key/cehamot
```

dan command ping tetap

```bash
$ ansible 34.128.82.28 -i ./inventory -m ping
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713632572634/5530012a-aa32-4e52-bca5-e7e5997972fd.png align="center")

command sebelumnya menggunakan pattern ***ansible &lt;pattern&gt; &lt;options&gt;***

**\-m atau -i** itu adalah options

[***Pattern***](https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html#patterns-targeting-hosts-and-groups) merupakan target host/group yang diinginkan.

ada beberapa cara penulisan pattern yang dapat di lihat [disini](https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html#common-patterns)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713632806244/fd2bdc25-1e20-494b-bdf7-90bc13a06d64.png align="center")

### Ansible Config

Ansible config digunakan untuk mengkonfigurasi behavior ansible saat kita menjalankan perintah-perintah ansible.

Ansible config di tulis dengan format **INI** dengan nama ansible.cfg,dan ansible config ini memiliki hirarki dalam pembacaan nya. [**Disini**](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#the-configuration-file)

Penulisan konfigurasi di ansible config di kelompokan dalam sebuah **Section.**

jadi contoh nya seperti ini,

```bash
$ ansible server_test -i inventory -m ping
```

pada command sebelumnya kita selalu mendefinisikan **option -i &lt;file-inventory&gt;** saat memanggil file inventory, dengan menggunakan **ansible.cfg** kita bisa menyederhanakan dengan menambahkan **key inventory** di section defaults.

```bash
[default]
inventory=./inventory
host_key_checking=False
```

satu hal lagi yang cukup penting menambahkan line **host\_key\_checking** yang fungsinya untuk menghindari host key checking saat pertama kali konek ke server baru.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713836417592/41ae49c6-9d6b-493d-bea1-020ed657402f.png align="center")

### Ansible Module

Pada materi sebelumnya kita selalu menggunakan option ***\-m ping,*** option yang kita gunakan tersebut nama nya **module.**

[**Module**](https://docs.ansible.com/ansible/latest/module_plugin_guide/modules_intro.html#introduction-to-modules) merupakan unit code untuk menjalankan suatu tugas tertentu.

Ansible punya banyak sekali module yang dapat kita gunakan [dokumentasi](https://docs.ansible.com/ansible/latest/collections/index_module.html#index-of-all-modules).

Jika module di eksekusi langsung di sebut [***adhoc command***](https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html#introduction-to-ad-hoc-commands)***.***

> * [ansible.builtin.add\_host](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/add_host_module.html#ansible-collections-ansible-builtin-add-host-module) – Add a host (and alternatively a group) to the ansible-playbook in-memory inventory
>     
> * [ansible.builtin.apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html#ansible-collections-ansible-builtin-apt-module) – Manages apt-packages
>     
> * [ansible.builtin.apt\_key](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html#ansible-collections-ansible-builtin-apt-key-module) – Add or remove an apt key
>     
> * [ansible.builtin.apt\_repository](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html#ansible-collections-ansible-builtin-apt-repository-module) – Add and remove APT repositories
>     
> * [ansible.builtin.assemble](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/assemble_module.html#ansible-collections-ansible-builtin-assemble-module) – Assemble configuration files from fragments
>     
> * [ansible.builtin.assert](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/assert_module.html#ansible-collections-ansible-builtin-assert-module) – Asserts given expressions are true
>     

Setiap **module** dapat memiliki argument untuk kebutuhan **module** itu sendiri, argument ini juga sering di sebut sebagai **parameter.**

salah satu contoh seperti ini:

```bash
$ ansible server_test -m command "date"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713841890039/4f6fb069-8bcd-47a1-93cc-1ab257dc0c5d.png align="center")

```bash
$ ansible server_test -m copy -a "src=./file.txt dest=/tmp/"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713842034740/01ef1f80-a2eb-4e15-9da4-0642923a8bf9.png align="center")