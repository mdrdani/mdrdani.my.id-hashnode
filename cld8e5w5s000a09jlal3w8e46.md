---
title: "Ansible Practice - Session 3"
seoTitle: "Ansible Practice - Session 3"
seoDescription: "Ansible Practice - Session 3"
datePublished: Wed Jan 18 2023 05:51:54 GMT+0000 (Coordinated Universal Time)
cuid: cld8e5w5s000a09jlal3w8e46
slug: ansible-practice-session-3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/yhCHx8Mc-Kc/upload/78eca4fc04869710ab8625fcae7b8fa3.jpeg
tags: ansible

---

Oke sampai di session 3 di session yang sebelum nya kita sudah membuat web landing page di 2 server, dan database mariadb. Sekarang kita akan coba menggunakan teknik load balancing agar traffic dari user tersebar pada 2 server yang sudah kita buat.

### Create Role Load Balancing

buat folder dengan nama **nginx-load-balancer,** didalamnya di buat folder **tasks.**

**main.yaml**

```yaml
- name: delete default config nginx
  file:
    path: '{{ item }}'
    state: absent
  loop:
    - /etc/nginx/sites-available/default
    - /etc/nginx/sites-enabled/default

- name: copy nginx config
  template: 
    src: templates/default.j2
    dest: /etc/nginx/sites-available/default

- name: symbolik link nginx
  file:
    src: /etc/nginx/sites-available/default
    dest: /etc/nginx/sites-enabled/default
    state: link
  notify:
    - reload nginx
```

selanjutnya kita buat folder **templates** di folder **nginx-load-balancer.** template ini merupakan konfigurasi dari load balancer nginx.

**default.j2**

```apache
upstream backend {
	{% for s in server %}
    server {{ s.host }};
  {% endfor %}
}

server {
	listen 80;
	server_name {{ domain }};

	location / {
		proxy_redirect off;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $http_host;
		proxy_pass http://backend;
	}
}
```

sehabis kita buat template selanjut nya kita buat reload nginx dengan folder **handlers** dan di dalam nya

**main.yaml**

```yaml
- name: reload nginx
  service: 
    name: nginx
    state: reloaded
```

jika sudah selanjutnya create playbook dari load balancer.

**load-balancer-playbook.yaml**

```yaml
- name: setup load balancer server
  hosts: load-balancer
  become: yes
  roles:
    - roles/update-os
    - roles/nginx
    - role: roles/nginx-load-balancer
      vars: 
          domain: landingpage.****.me
          server:
            - name: landing-page-1
              host: ***
            - name: landing-page-2
              host: ****
```

yang \*\*\* sesuaikan dengan konfigurasi yang di perlukan.

```bash
~/studi-kasus$ ansible-playbook -i inventory load-balancer-playbook.yaml
```

Tunggu dan coba akses kembali web server, refresh browser F5 akan terlihat IP berpindah2 tanda nya load balancer sudah aktif.

Mohon di pahami ya jangan langsung copas2 saja konfigurasi yang sudah di buat.

Sekiaan..