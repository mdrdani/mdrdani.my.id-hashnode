---
title: "Ansible Fundamental - Sesi 2"
datePublished: Wed Jan 11 2023 17:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clvkqo7b000090ajq5b1n3g1i
slug: ansible-fundamental-sesi-2
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/WGeO6dW5GZM/upload/0e05032fbda9f120a12700a44abe066c.jpeg
tags: linux, ansible, google-cloud-platform

---

### Ansible Playbook

Dalam praktek lapangan kita akan banyak menjalankan banyak module/tugas sekaligus, di sini untuk mempermudah masalah ini kita bisa menggunakan [**Ansible Playbook**](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html#ansible-playbooks) dimana kita mendefinisikan banyak module dalam satu file **YAML.**

Oke selanjutnya kita coba buat file **Ansible Playbook** yang tugas awal ini kita menginstall Web server Nginx dan menjalankan file index.html,buatlah file dengan nama **playbook-webserver.yaml.**

```yaml
- name: Playbook setup web server
  hosts: server_test
  become: true
  become_method: sudo
  tasks:
    - name: Update repository
      ansible.builtin.apt:
        update_cache: true
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
    - name: Start Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
    - name: Copy file html
      ansible.builtin.copy:
        src: ./web/
        dest: /var/www/html/
        mode: '604'
```

jika sudah di buat jalankan dengan perintah

```yaml
$ ansible-playbook playbook-webserver.yaml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713857683392/0408d9b4-5044-45d4-be0c-e8e4e0ab38ed.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714228503871/954a497b-5c20-4c7f-a1ca-fcf8b887de95.png align="center")

### Ansible Playbook Variables

Terkadang kita memerlukan konfigurasi kepada hal yang sama, contoh kita membuat nama user pada \*\*task 1,\*\*pada **task ke 2** kita membuat grub dengan nama user yang sama kita dapat membuat nya sebagai [variables](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html) untuk mendefinisikan sekali saja.

**playbook-webserver-vars.yaml**

```yaml
- name: Playbook setup web server
  hosts: server_test
  become: true
  vars: # mendefinisikan variable
    user_app: ansibleweb
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
    - name: Buat User {{ user_app }}
      ansible.builtin.user:
        name: "{{ user_app }}"
        password: belajaransible
        shell: /bin/bash
    - name: Copy file html
      ansible.builtin.copy:
        src: ./web/
        dest: /var/www/html/
        mode: '604'
        owner: "{{ user_app }}"
        group: "{{ user_app }}"
```

```yaml
$ ansible-playbook playbook-webserver-vars.yaml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714231825315/07626647-1da9-4a61-978d-c483328bfe88.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714232462930/d884f401-5641-4b0b-bf37-cf27a1898321.png align="center")

terlihat file **index.html** sudah kita rubah owner menggunakan **user ansibleweb dan group ansibleweb.**

karena playbook menggunakan format **YAML** sebenarnya kita bisa menggunakan variable dalam bentuk [boolean](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#boolean-variables), [list](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#list-variables) dan [dictionary](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#dictionary-variables) jika dibutuhkan.

### Ansible Conditional

Terkadang pada kondisi tertentu kita menemui task yang bisa menyesuaikan keadaan yang kita tuliskan di playbook. Contoh menginstall Nginx di distro Ubuntu/debian menggunakan **APT,** namun jika di distro centos/redhat meggunakan **YUM.**

Ansible memiliki kemampuan untuk membuat semacam "if-else" di pemrograman yang bernama [Conditional](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html#conditionals).

Sama seperti if conditionals pada umumnya kita dapat menggunakan operasi logical operator seperti *and, or, not* dan comparision operator seperti &gt;=, &lt;=,==

**playbook-webserver-when.yaml**

```yaml
---
- name: Playbook setup web server
  hosts: server_test
  become: true
  gather_facts: true # defaultnya memang true
  vars: # mendefinisikan variable
    user_app: ansibleweb
  tasks:

    ## Install nginx
    - name: Install nginx (Debian)
      ansible.builtin.apt:
        name: nginx
        state: present
      when:
        - ansible_os_family == "Debian"
        - ansible_processor_cores >= 0.25 or ansible_memory_mb.real.total >= 512
    - name: Install nginx (Alpine)
      community.general.apk:
        name: nginx
        state: present
      when:
        - ansible_os_family == "Alpine"
        - ansible_processor_cores >= 0.25 or ansible_memory_mb.real.total >= 512

    ## Buat user
    - name: Buat user (Debian) {{ user_app }}
      ansible.builtin.user:
        name: "{{ user_app }}"
        password: belajaransible
        shell: /bin/bash
      when:
        - ansible_os_family == "Debian"
    - name: Buat user (Alpine) {{ user_app }}
      ansible.builtin.user:
        name: "{{ user_app }}"
        password: belajaransible
        shell: /bin/sh
      when:
        - ansible_os_family == "Alpine"

    ## Copy file html
    - name: Copy file html (Debian)
      ansible.builtin.copy:
        src: ./web/
        dest: /var/www/html/
        mode: '604'
        owner: "{{ user_app }}"
        group: "{{ user_app }}"
      when:
        - ansible_os_family == "Debian"
    - name: Copy file html (Alpine)
      ansible.builtin.copy:
        src: ./web/
        dest: /usr/share/nginx/html
        mode: '604'
        owner: "{{ user_app }}"
        group: "{{ user_app }}"
      when:
        - ansible_os_family == "Alpine"

```

```yaml
$ ansible-playbook playbook-webserver-when.yaml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714374231691/eb47b895-7f09-4b9f-ba8e-009c8a50ff0f.png align="center")

Secara default saat menjalankan playbook, Ansible akan mengambil informasi dari server tujuan terelebih dahulu sebelum mejalankan task yang akan dikerjakan.

Informasi tersebut diambil dan di simpan dalam sebuah variable yang dinamakan [Facts](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html#ansible-facts). Jika ingin mengececek **Facts** secara manual untuk melihat informasi bisa menggunakan perintah *ansible &lt;pattern/host&gt; -m ansible.builtin.setup.*

```yaml
$ ansible server_test -m ansible.builtin.setup
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714373127699/d71b2dc6-906c-42bc-b956-d72a68e8f3b4.png align="center")

Terkadang kita hanya ingin menjalankan task task tersentu yang ada di suatu playbook. Misal kita sudah menginstall web server nginx, jadi kita hanya ingin menjalankan task copy file html untuk menghemat waktu.

Untuk mengatasi ini kita dapat menggunakan [Tags](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_tags.html#tags).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714378974992/72bd4767-2f32-4000-8ca2-a5d62335e943.png align="center")

untuk menjalankan nya kita dapat menggunakan command

```yaml
$ ansible-playbook playbook-webserver-when.yaml --check --tags copy_file
```

atau bisa skip task selain yang di tentukan

```yaml
$ ansible-playbook playbook-webserver-when.yaml --check --skip-tags copy_file
```

### Ansible Loops

Pada ansible ada yang nama nya [**Loops**](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html)**,** yang guna nya untuk menghindari duplikasi atau redundan dari konfigurasi YAML kita.

Contoh kita install php8.2, php8.2-cli, dll, nah ini kita membuat 3 task untuk menginstall 3 package tersebut.

**playbook-php-loop.yaml**

```yaml
---
- name: Playbook setup PHP
  hosts: server_belajar
  become: true
  gather_facts: true # defaultnya memang true
  vars:
    taget_php_version: 8.2
  tasks:
    - name: Add repository for PHP
      ansible.builtin.apt_repository:
        repo: 'ppa:ondrej/php'
        state: present
      tags: prepare

    - name: Update repo
      ansible.builtin.apt:
        update_cache: true
      tags: prepare

    - name: Install php {{ taget_php_version }}
      ansible.builtin.apt:
        name: "{{ item }}"
        state: present
      with_items:
        - php{{ taget_php_version }}
        - php{{ taget_php_version }}-cli
        - php{{ taget_php_version }}-common
        - php{{ taget_php_version }}-imap
        - php{{ taget_php_version }}-redis
        - php{{ taget_php_version }}-xml
        - php{{ taget_php_version }}-zip
        - php{{ taget_php_version }}-mbstring
        - php{{ taget_php_version }}-curl
        - php{{ taget_php_version }}-gd
        - php{{ taget_php_version }}-bcmath
        - php{{ taget_php_version }}-gmp
        - php{{ taget_php_version }}-mysqli
      tags:
        - install
```

```yaml
$ ansible-playbook playbook-php-loop.yaml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714380524455/302606cf-ccdd-4131-8f9e-8fd7de735e25.png align="center")

### Ansible Vault

Pada Playbook sebelumnya kita membuat user beserta **password** secara transparan, menulis informasi yang sensitif tidak di izinkan apalagi jika di production.

Untuk memproteksi hal yang sifatnya sensitif sebaiknya kita menggunakan [Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html#protecting-sensitive-data-with-ansible-vault) di ansible. Dengan menggunakan **Vault** data2 yang senstif tersebut akan di enkripsi sehingga jauh lebih aman.

```yaml
$ ansible-vault create secret-user.yaml
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714381100612/6570e7bd-2e1f-4177-beee-0c93cf0ab5bf.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714381089380/592b9ffa-f0f0-4cca-96ef-56be5632ccc9.png align="center")

nanti akan terbentuk file baru dengan nama **secret-user.yaml** yang isinya akan angka acak seperti ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714381154386/6cb1631c-8f47-4029-bf7b-c571d93f6de5.png align="center")

Setelah kita mendefinisikan varibale di encrypted file selanjutnya bagaimana kita memanggil variable tersebut di file playbook kita

Agar variable tersebut dapat digunakan di playbook kita perlu melakukan parsing variable tersebut dengan module **ansible.builtin.include\_vars.**

**playbook-vault.yaml**

```yaml
---
- name: Playbook buat user baru
  hosts: server_test
  become: true
  gather_facts: true # defaultnya memang true
  vars: # mendefinisikan variable
    user_app: userbaru
  tasks:

    - name: Parsing variable dari secret file
      ansible.builtin.include_vars:
        file: secret-user.yaml

    - name: Add new user
      ansible.builtin.user:
        name: "{{ user_app }}"
        # password: belajaransible # gak secure kita ganti pake Ansible Vault

        password: "{{ user_pass | password_hash('sha512') }}" # ambil value dari variable lalu lakukan hash
        shell: /bin/bash
      when:
        - ansible_os_family == "Debian"
```

```yaml
$ ansible-playbook playbook-vault.yaml --ask-vault-pass
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1714381579501/56aa2081-aed1-47d1-93cb-51a3293b123b.png align="center")

jika kita perlu mengedit isi dari file yang enkripsi bisa dengan command

```yaml
$ ansible-vault edit secret-user.yaml
```

atau melihat isi dari file

```yaml
$ ansible-vault view secret-user.yaml
```