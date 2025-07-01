---
title: "Install Jenkins to AWS EC2 Instance With Ansible"
datePublished: Wed Jul 19 2023 14:52:09 GMT+0000 (Coordinated Universal Time)
cuid: clk9udz8w000509l9hfyfeaq7
slug: install-jenkins-to-aws-ec2-instance-with-ansible
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/p0j-mE6mGo4/upload/a2c745392dff93d4a4a12a35b95d9d4d.jpeg
tags: aws, ansible, jenkins

---

Sesudah create AWS EC2 Instance menggunakan Terraform, selanjut nya install App Jenkins menggunakan Ansible, Struktur folder Ansible akan seperti ini.

![](https://file.notion.so/f/s/18e099da-b88b-41ce-8a3e-95f4f4b881e4/Untitled.png?id=195b6416-9839-4210-ac72-f5886ecfeb62&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=lJAMf80saAUoaz-yWKnZAX7xYG7sCjnltmqOUSE8VI4&downloadName=Untitled.png align="center")

**inventory**

```bash
[ip-server]
18.143.101.**

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/cehamot/BigprojectStudiDevops/mdrdani.pem
```

**roles/update-OS/tasks/main.yaml**

```yaml
- name: update server linux Debian/Ubuntu
  shell: apt update
  when: ansible_distribution == 'Debian' or ansible_distribution == 'Ubuntu'


- name: update server linux redhat/centos
  shell: apt update
  when: ansible_distribution == 'CentOs' or ansible_distribution == 'Red Hat Enterprise Linux'
```

**roles/Jenkins/tasks/main.yaml**

```yaml
- name: Import Jenkins key from URL
    ansible.builtin.get_url:
      url: https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
      dest: /usr/share/keyrings/jenkins-keyring.asc
  
  - name: Dowload Long term jenkins release
    ansible.builtin.apt_repository:
      repo: "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/"
      state: present

  - name: Update Repository
    shell: apt update
  
  - name: Install Jenkins
    apt:
      name: jenkins
      state: latest
  
  - name: daemon-reload to pick up config changes
    ansible.builtin.systemd:
      daemon_reload: yes

  - name: start jenkins
    ansible.builtin.systemd:
      name: jenkins
      state: started
```

**roles/java/tasks/main.yaml**

```yaml
- name: Install Java
    become: yes
    apt:
        name: "{{ packages }}"
        state: present
    vars:
        packages:
           - openjdk-11-jre
```

**roles/docker/tasks/main.yaml**

```yaml
- name: Install depedencies
    apt:
      name: "{{item}}"
      state: present
      update_cache: yes
    loop:
      - apt-transport-https
      - ca-certificates
      - curl
      - gnupg-agent
      - software-properties-common
  
  - name: Docker Official GPG key
    apt_key:
      url: https://download.docker.com/linux/ubuntu/gpg
      state: present

  - name : Repository Docker
    apt_repository:
      repo: deb https://download.docker.com/linux/ubuntu focal stable
      state: present
  
  - name: Install Docker
    apt:
      name: "{{item}}"
      state: latest
      update_cache: yes
    loop:
      - docker-ce
      - docker-ce-cli
      - containerd.io

  - name: add user mod
    command: sudo usermod -aG docker jenkins

  - name: Make Sure docker is running
    service:
      name: docker
      state: started
      enabled: yes
```

**server-jenkins-playbook.yaml**

```yaml
- name: Server Jenkins
  hosts: ip-server
  become: yes
  roles:
    - roles/update-os
    - roles/java
    - roles/jenkins
    - roles/docker
```

run

```bash
$ ansible-playbook -i inventory server-jenkins-playbook.yaml
```

![](https://file.notion.so/f/s/557cd194-3bb1-4f41-a749-2e204221fc5a/Untitled.png?id=0ce8665c-2e73-44ca-91ff-2c9184598989&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=B1BGViy7yz0n8ZchhvwvVxhYQGs8CNycrQbWfRxzC-A&downloadName=Untitled.png align="left")

Oke Jenkins sudah running, selanjutnya akses menggunakan browser ke IP Public instance. Jika belum bisa di akses seperti gambar di bawah ini maka kita harus setting security instance nya untuk allow port 8080.

![](https://file.notion.so/f/s/0044203d-d7b1-404f-8709-b0a49195a9f7/Untitled.png?id=29659d0f-db18-479d-8323-4f637c5ca255&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=D82vmBP4aywK3w7BHLbfpfs4uwVSUdqmw554abSkNV8&downloadName=Untitled.png align="left")

Ke bagian Security Groups, Edit Inbound tambahkan seperti ini. Save rules

![](https://file.notion.so/f/s/bd1683e5-a8d0-46b3-ba21-e5b3e58659a1/Untitled.png?id=32c20810-fc26-41bc-859d-27897292576d&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=8a3XKpvyNu8E4xv-QZ0vvHfuJVqB70fipZoIIadJnjM&downloadName=Untitled.png align="left")

balik ke browser lagi akses menggunakan port 8080

![](https://file.notion.so/f/s/178e69fe-c331-43a9-87ca-40bf9a9941b0/Untitled.png?id=2920bf06-19f3-46aa-b134-5d3f0f71ce73&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=kiITkJTqit5x5K4FjLNfCI68Ua_XbL9WzH3ijlFiSqA&downloadName=Untitled.png align="left")

untuk unlock jenkins kita harus membuka file /var/lib/jenkins/secrets/initialAdminPassword di instance.

![](https://file.notion.so/f/s/07c15320-a217-4eff-a553-8044d8f4a258/Untitled.png?id=5fbf4c7f-5ce9-44aa-a1a3-29a57058258d&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=8M3zG6pJm4lDAL5jBHtxOQA1QWyMrzRSb5P5D16GBio&downloadName=Untitled.png align="left")

copy kan password ke dalam kolom Administrator password tersebut dan continue.

![](https://file.notion.so/f/s/94c89f9c-6675-4e06-9ae9-028abe7e1e9e/Untitled.png?id=652f982f-50f2-4366-baaa-ffffd8e87b31&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=6h2M9PtPPhpkgNhJ92oRM6KZsckITHW3SgzeoCv8OnM&downloadName=Untitled.png align="left")

Pilih saja **install suggested plugins** nanti akan di install plugin2 yang biasa nya di perlukan. Masukan username, password dan full name. Save and continue.

![](https://file.notion.so/f/s/a52f37ea-2a23-41e4-859c-ed9286cfbc7a/Untitled.png?id=090d13fa-ccca-4e83-bd4e-98266d83c4fb&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=Z8Wb0ve9JsHl58vB5UvpNF1BdE2fj1ealeq5BTszd6c&downloadName=Untitled.png align="left")

sampai di sini pastikan URL sama dengan yang link browser di atas nya. save and finish

**Yey Start using jenkins !!**

![](https://file.notion.so/f/s/4a1967de-bc92-4c73-8085-cf981776482f/Untitled.png?id=fb58d9ef-d069-4616-be64-5b2fec0d3a17&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=xIx7poqIYWl_oBSRgN3RSoSNd4mfSNJOKmBH2NSV2uY&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/8ebfcf50-175a-4517-bb99-73305b7b8034/Untitled.png?id=04f3a439-ea0e-47ea-8cf9-65c98cf38856&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=61pwD0ZVYJKyC3l7qtx4TkzWxRcVseRaZWJay0PlmOA&downloadName=Untitled.png align="left")