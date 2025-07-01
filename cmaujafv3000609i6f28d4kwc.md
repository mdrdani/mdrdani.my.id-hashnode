---
title: "Running Three-Tier Architecture App on Virtual Machine with Vagrant"
seoTitle: "Running Three-Tier Architecture App on Virtual Machine with Vagrant"
seoDescription: "Dalam pengembangan aplikasi modern, arsitektur three-tier (tiga lapis) adalah pendekatan yang umum digunakan karena memisahkan logika aplikasi menjadi tiga"
datePublished: Mon May 19 2025 03:36:58 GMT+0000 (Coordinated Universal Time)
cuid: cmaujafv3000609i6f28d4kwc
slug: running-three-tier-architecture-app-on-virtual-machine-with-vagrant
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/VBLHICVh-lI/upload/d48599f2c4186e58df4b19fc2eb4a25c.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1751271407322/8a016073-b1d9-4a58-8846-71d4a96dd69a.png
tags: ubuntu, vagrant, nginx, nodejs, reactjs

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747582464576/6fa968e5-9263-444c-a36c-bb596ab186ff.png align="center")

Dalam pengembangan aplikasi modern, arsitektur **three-tier** (tiga lapis) adalah pendekatan yang umum digunakan karena memisahkan logika aplikasi menjadi tiga komponen utama: **frontend**, **backend**, dan **database**. Namun, seiring bertambahnya kompleksitas sistem, kebutuhan akan pengelolaan **infrastruktur yang otomatis dan konsisten** menjadi semakin penting.

Melalui artikel ini, kita akan membahas implementasi arsitektur three-tier yang tidak hanya modular, tetapi juga **sepenuhnya dapat diotomatisasi** menggunakan **Vagrant** dan **VirtualBox**. Pendekatan ini sangat cocok untuk tim pengembang yang menginginkan lingkungan lokal yang stabil dan mudah direplikasi.

* **Frontend**: Dibangun dengan React.js
    
* **Backend**: Menggunakan Node.js
    
* **Database**: PostgreSQL
    
* **Load Balancer**: Dikelola oleh NGINX
    
* **Virtualisasi**: Semua komponen dijalankan dalam **VM** yang dikelola oleh **Vagrant** dan **VirtualBox**
    

## Arsitektur Sistem

### 1\. **User (Client)**

Pengguna mengakses aplikasi melalui browser. Semua permintaan dari client diarahkan terlebih dahulu ke server NGINX yang bertindak sebagai **load balancer**.

### 2\. NGINX sebagai Load Balancer

NGINX berada di garis depan sistem dan berperan sebagai **load balancer**, yaitu komponen yang bertugas untuk **mendistribusikan permintaan dari pengguna ke server tujuan yang sesuai**. Dalam arsitektur ini, NGINX memiliki fungsi utama:

* **Menerima request dari user**
    
* **Meneruskan permintaan ke Frontend VM** jika permintaan adalah halaman UI
    
* **Meneruskan permintaan ke Backend VM** jika permintaan adalah API
    
* **Menyediakan SSL termination** *(opsional, jika HTTPS digunakan)*
    
* **Menyembunyikan VM internal dari akses langsung pengguna**
    
* **Meningkatkan skalabilitas dan keandalan sistem** dengan mengatur lalu lintas berdasarkan peran server
    

### 3\. **Frontend VM (React.js)**

VM ini menjalankan aplikasi React yang merupakan antarmuka pengguna (UI). Semua aset statis seperti HTML, CSS, dan JavaScript di-serve dari sini. React akan mengirimkan permintaan API ke backend menggunakan endpoint yang sudah ditentukan.

### 4\. **Backend VM (Node.js)**

VM ini menjalankan server aplikasi berbasis **Node.js**. Backend memproses logika bisnis, validasi, autentikasi, dan komunikasi dengan database.

Contoh fungsi backend:

* Login user
    
* Pemrosesan form
    
* Pengambilan data produk
    
* Penyimpanan data baru ke database
    

### 5\. **Database VM (PostgreSQL)**

Menyimpan data aplikasi seperti user, transaksi, atau konten lainnya. Backend akan berkomunikasi langsung dengan VM ini melalui koneksi database.

### 6\. **Vagrant + VirtualBox**

Seluruh komponen (Frontend, Backend, Database) dijalankan dalam virtual machine yang dikelola oleh **Vagrant**, dengan **VirtualBox** sebagai provider VM.

Keuntungan menggunakan Vagrant dengan VirtualBox:

* **Automasi provisioning**: Semua VM bisa dibuat dan dikonfigurasi hanya dengan satu perintah (`vagrant up`)
    
* **Isolasi lingkungan**: Setiap lapisan berjalan di VM terpisah
    
* **Jaringan virtual**: Dapat diatur menggunakan IP statis via `private_network` atau `host-only`
    
* **Lingkungan konsisten**: Developer tidak perlu menginstal layanan manual di OS host
    
* **Portabilitas**: Konfigurasi yang sama bisa dijalankan di mesin berbeda
    

### Vagrantfile

Konfigurasi `Vagrantfile` di bawah adalah bagian dari arsitektur infrastruktur otomatis menggunakan **Vagrant** dan **VirtualBox**, yang menetapkan **pengaturan umum** untuk semua VM dalam proyek.

```bash
Vagrant.configure("2") do |config|
  # Configure common settings

  # Base box
  config.vm.box = "bento/ubuntu-22.04"
  
  # Configure VirtualBox related settings
  config.vm.provider "virtualbox" do |vb|
    # Set CPU and Memory of the VM
    vb.memory = "768"
    vb.cpus = 2
  end

  # I don't want to check for updates
  config.vm.box_check_update = false
  
  # Disable the default share of the current code directory
  # I don't want the guest to write to make changes to the code
  config.vm.synced_folder ".", "/vagrant", disabled: true
  
  # Common provisioning script for all VMs
  config.vm.provision "shell", inline: "apt update && apt install -y curl git gnupg net-tools"
  
  # Common provisioning script to install NodeJS v18
  # It will be used in frontend and backend VMs
  nodejs_install_script = <<-SHELL
    echo "Installing NodeJS v18"
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
    nvm install 18
    echo "NodeJS version: $(node -v)"
    echo "NPM version: $(npm -v)"
    sudo npm install -g pm2 npm
  SHELL
.......
```

Berikut penjelasan tiap bagian:

```bash
Vagrant.configure("2") do |config|
```

Menandakan kita menggunakan **Vagrantfile format versi 2**, yang merupakan standar saat ini.

```bash
config.vm.box = "bento/ubuntu-22.04"
```

Mengatur **sistem operasi dasar** semua VM menjadi **Ubuntu 22.04** dari provider bento.

```bash
config.vm.provider "virtualbox" do |vb|
  vb.memory = "768"
  vb.cpus = 2
end
```

Mengatur resource untuk setiap VM:

* RAM: 768 MB
    
* CPU: 2 core
    

Ini memastikan semua VM berjalan ringan, cocok untuk lokal.

```bash
config.vm.box_check_update = false
```

Menonaktifkan pengecekan update box setiap kali `vagrant up`, agar lebih cepat.

```bash
config.vm.synced_folder ".", "/vagrant", disabled: true
```

Menonaktifkan **folder sinkronisasi default** antara host (`.`) dan guest (`/vagrant`), biasanya untuk mencegah **tulisan tak diinginkan ke host**, atau menjaga isolasi.

```bash
config.vm.provision "shell", inline: "apt update && apt install -y curl git gnupg net-tools"
```

Script shell ini akan dijalankan **di semua VM**, menginstal alat bantu dasar:

* `curl`, `git`, `gnupg`, dan `net-tools` untuk keperluan umum sistem
    

```bash
nodejs_install_script = <<-SHELL
    echo "Installing NodeJS v18"
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
    nvm install 18
    echo "NodeJS version: $(node -v)"
    echo "NPM version: $(npm -v)"
    sudo npm install -g pm2 npm
  SHELL
```

Script ini mendefinisikan **instalasi Node.js versi 18** menggunakan **NVM (Node Version Manager)**, dan akan digunakan dalam provisioning **Frontend** dan **Backend VM**.

Rincian tugas:

1. **Install NVM**
    
2. **Install Node.js v18**
    
3. **Verifikasi versi node dan npm**
    
4. **Global install:**
    
    * `pm2`: Untuk menjalankan proses Node.js sebagai background service
        
    * `yarn`: Sebagai alternatif `npm` yang populer dan cepat
        

---

Setelah itu file konfigurasi di bawah adalah lanjutan dari `Vagrantfile` diatas yang mendefinisikan **VM untuk Load Balancer** dengan peran sebagai **NGINX load balancer**.

```bash
.....
# Define Load Balancer VM
  config.vm.define "lb" do |lb|
    lb.vm.hostname = "lb"
    lb.vm.network "private_network", ip: "192.168.56.5"
    lb.vm.provision "shell", inline: <<-SHELL
    
    echo "Installing Nginx"
    apt install -y nginx
    
    echo "Configuring Nginx as a Load Balancer"
cat > /etc/nginx/sites-available/default << EOF
  server {
      listen 80;
      server_name _;

      # Forward requests to /api to the backend
      location ~* /api {
          proxy_pass http://192.168.56.3:3000;
      }
      
      # Forward other requests to the frontend
      location / {
          proxy_pass http://192.168.56.2;
      }
  }
EOF
    echo "Restart NGINX to apply changes"
    systemctl restart nginx
    SHELL
  end
.....
```

Menjadikan VM ini sebagai **entry point** untuk semua request pengguna. NGINX bertindak sebagai **router** yang mengarahkan request ke frontend atau backend sesuai path URL.

Singkatnya:  
✅ **Automatisasi setup NGINX**  
✅ **Routing berdasarkan path**  
✅ **Terintegrasi dalam arsitektur 3-tier yang dijalankan via Vagrant**

untuk menjalankan **VM Load Balancer** kita dapat menggunakan perintah

```bash
vagrant up lb
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747578806625/156bc048-5c3d-4cea-b263-25b0752251af.png align="center")

jika sudah **running** kita dapat check menggunakan browser dengan mengakses IP Address **VM Load Balancer** tersebut. status nya masih **502 Bad Gateway** karena VM lain nya belum di aktifkan.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747578917683/856f553f-fd39-483a-aba3-08fb643443fb.png align="center")

jika kita ingin mengakses **VM Load Balancer** dengan menggunakan **SSH** kita dapat menggunakan command

```bash
vagrant ssh lb
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747579115077/0925ec64-5fe1-417b-a752-8a0d7b4f9662.png align="center")

---

Selanjutnya kita konfigurasi **VM Database** masih lanjutan dari `Vagrantfile` . **VM Database** menggunakan **PostgreSQL versi 16.**

```bash
......
config.vm.define "db" do |db|
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.56.4"
    db.vm.provision "shell", inline: <<-SHELL
      sudo apt install -y postgresql-common
      sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
      sudo apt -y install postgresql-16
      echo "Start PostgreSQL service"
      systemctl start postgresql

      echo "Enable PostgreSQL to start on boot"
      systemctl enable postgresql

      echo "Set postgres user password to postgres, for demonstration purposes"
      sudo -u postgres psql -c "ALTER ROLE postgres WITH password 'postgres'"

      echo "Create the conduit database owned by postgres"
      sudo -u postgres psql -c "CREATE DATABASE conduit OWNER postgres;"

      echo "Configure PostgreSQL to listen on all interfaces"
      sed -i "s/#listen_addresses = 'localhost'/listen_addresses = '*'/g" /etc/postgresql/16/main/postgresql.conf

      echo "Allow all connections in pg_hba.conf"
      echo "host    all             all             0.0.0.0/0               md5" | sudo tee -a /etc/postgresql/16/main/pg_hba.conf

      echo "Restart PostgreSQL to apply changes"
      systemctl restart postgresql
    SHELL
  end
.....
```

untuk menjalankan **VM Database** kita dapat menggunakan perintah

```bash
vagrant up db
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747580179723/25cf7a7f-b5ee-4ef2-9b3f-8d2130839328.png align="center")

untuk mengakses **VM Database** seperti biasa kita bisa gunakan command

```bash
vagrant ssh db
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747580368141/681e2acb-8711-4f52-ad04-3b6004941795.png align="center")

---

Selanjutnya kita konfigurasi **VM Backend** masih lanjutan dari `Vagrantfile` dengan IP `192.168.56.3` **VM Backend** ini bertugas menjalankan **backend service node.js** yang di ambil dari github [https://github.com/gothinkster/node-express-realworld-example-app.git](https://github.com/gothinkster/node-express-realworld-example-app.git) **.**

```bash
.....
config.vm.define "backend" do |backend|
    backend.vm.hostname = "backend"
    backend.vm.network "private_network", ip: "192.168.56.3"
    backend.vm.provision "shell", privileged: false, inline: nodejs_install_script
    backend.vm.provision "shell", privileged: false, inline: <<-SHELL
      echo "Begin installing Backend"
      export NVM_DIR="$HOME/.nvm"
      [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
      export DATABASE_URL=postgresql://postgres:postgres@192.168.56.4:5432/conduit
      export JWT_SECRET=6b350c623d150e8a07d36e963e1416079a7a322e8c04952d674fe607914d7b01
      export NODE_ENV=development
      echo "Cloning the backend repository"
      git clone https://github.com/gothinkster/node-express-realworld-example-app.git $HOME/backend

      echo "Writing environment variables to .env"
      cat > $HOME/backend/.env << EOF
DATABASE_URL=postgresql://postgres:postgres@192.168.56.4:5432/conduit
JWT_SECRET=6b350c623d150e8a07d36e963e1416079a7a322e8c04952d674fe607914d7b01
NODE_ENV=development
EOF

      echo "Installing dependencies"
      cd $HOME/backend
      npm install

      echo "Generating Prisma client, running migrations, and seeding the database"
      npx prisma generate
      npx prisma migrate deploy && npx prisma db seed
      
      echo "Starting the backend server using pm2"
      pm2 start npm --name "backend" -- start
    SHELL
  end
.....
```

untuk menjalankan **VM Backend** kita dapat menggunakan perintah

```bash
vagrant up backend
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747581508970/875bcb8e-5df8-4aa5-81fa-30a18926c784.png align="center")

untuk mengakses **VM Backend** seperti biasa kita bisa gunakan command

```bash
vagrant ssh backend
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747581646499/bb8ffee7-0782-4f45-8655-83c8a10bf311.png align="center")

---

Terakhir kita konfigurasi **VM Frontend** masih lanjutan dari `Vagrantfile` dengan IP `192.168.56.`2\. **VM Frontend** ini bertugas menjalankan **frontend service React.js** yang di ambil dari github [https://github.com/khaledosman/react-redux-realworld-example-app.git](https://github.com/khaledosman/react-redux-realworld-example-app.githttps://github.com/khaledosman/react-redux-realworld-example-app.git).

```bash
......
config.vm.define "frontend", autostart: false do |frontend|
    frontend.vm.hostname = "frontend"
    frontend.vm.network "private_network", ip: "192.168.56.2"
    # Copy frontend directory to VM. 
    frontend.vm.provision "shell", inline: "apt install -y nginx"
    frontend.vm.provision "shell", privileged: false, inline: nodejs_install_script
    frontend.vm.provision "shell", privileged: false, inline: <<-SHELL
      echo "Begin installing Frontend"
      export NVM_DIR="$HOME/.nvm"
      [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
      
      echo "Cloning the frontend repository"
      git clone https://github.com/khaledosman/react-redux-realworld-example-app.git $HOME/frontend
      
      echo "Installing dependencies"
      cd $HOME/frontend/
      npm install

      echo "Building the frontend"
      REACT_APP_BACKEND_URL="http://192.168.56.5/api" NODE_ENV=production npm run build

      echo "Copying the build to /var/www/html to serve it using Nginx"
      sudo mv $HOME/frontend/build/* /var/www/html/ 
    SHELL
  end
end
```

untuk menjalankan **VM Frontend** kita dapat menggunakan perintah

```bash
vagrant up frontend
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747582535842/41a220ef-b906-495b-9523-0ddee04b79c3.png align="center")

untuk mengakses **VM Frontend** seperti biasa kita bisa gunakan command

```bash
vagrant ssh frontend
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747582669255/4e528f6e-8c5a-4ae8-bec6-b00cd5a69f40.png align="center")

setelah semua VM up maka kita akan check kembali ke browser dengan akses [http://192.168.56.5/](http://192.168.56.5/)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747622413860/557e239b-55d6-4047-a49b-f5938637b1e4.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747622802658/6a0401ae-2681-44b2-ace0-c161c74f2e8e.gif align="center")

Bagaimana kita tau bahwa **three tier architecture** kita berjalan?? cara nya gampang matikan salah satu VM apakah masih berjalan dengan baik, seharusnya tidak bisa di akses kita contoh kan di matikan **VM Backend** dengan perintah.

```bash
vagrant halt backend
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1747622954433/ed06e43d-b478-4947-bcd4-18f5292ffac0.png align="center")

terlihat **VM Frontend** tidak dapat mengakses **API** **VM Backend.**  
untuk mematikan semua VM kita dapat dengan mudah menggunakan command

```bash
vagrant halt
```

---

Dengan menerapkan arsitektur **three-tier** menggunakan **NGINX sebagai load balancer**, **Node.js untuk backend**, **React sebagai frontend**, dan **PostgreSQL sebagai database**, kita telah membangun lingkungan aplikasi web yang **modular, terstruktur, dan mudah dikembangkan**.

Penggunaan **Vagrant** dan **VirtualBox** memungkinkan kita untuk **mengotomasi infrastruktur** secara konsisten, sehingga setiap anggota tim dapat mereplikasi lingkungan pengembangan dengan satu perintah. Hal ini sangat penting dalam pengembangan perangkat lunak modern yang menuntut efisiensi, konsistensi, dan skalabilitas.

Github : [https://github.com/mdrdani/three-tier](https://github.com/mdrdani/three-tier)