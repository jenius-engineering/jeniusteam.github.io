---
layout: post
title: "Ansible dasar"
author: "dedenf"
tags:
    - ansible
    - unix
    - devops
    - provisioning

categories: 
    - article
    - howto
    - ansible
    - devops

published: true
---

Belajar menggunakan [Ansible](https://ansible.com/) untuk mengatur mesin remote atau untuk provisioning.

### Install
Untuk macOS, bisa langsung pakai homebrew untuk instalasi

```shell
$ brew install ansible
```
Jika sukses di-instal, kita test dengan perintah 
```shell
$ ansible --version
ansible 2.7.0
```
<!-- more -->
### Konfigurasi
Untuk contoh sederhana ini, kita buat dengan menuliskan konfigurasi file yang berisi *host* target yang akan kita *provisioning*, di dalam folder project kita,
```shell
$ touch hosts
```

Kemudian `vi hosts` dan masukkan daftar *host* yang akan digunakan, di sini menggunakan host yang telah saya buat sebelumnya di [digitalocean](https://m.do.co/c/714b3c32b2e7), menggunakan Terraform (!TODO nulis), contoh.

```ini
[webserver]
123.123.123.123
```
Kemudian tiba dihal yang penting, membuat ansible playbook, playbook memiliki format `.yml` yang mana lebih mudah dimengerti daripada `json` atau format lainnya, `xml` misalkan.

#### `site.yml`
Kita buat file tersebut 
```shell
$ vim site.yml
```
Untuk isinya, saya coba tulis yang sederhana terlebih dahulu.

```YAML
---
- name: Install web server
  hosts: all
  remote_user: root

  tasks:
    - name: Install mariadb server
      apt:
        name: mariadb-server
      register: mariadbinstall
    - name: Uninstall mariadb server
      apt:
        name: mariadb-server
        state: absent
       register: mariadbuninstall

```

Save file, kemudian jalankan perintah
```shell
$ ansible-playbook -i hosts site.yml
```

Dan seharusnya akan keluar `stdout`

```shell
PLAY [Install web server] *****************************************************************************************

TASK [Gathering Facts] *****************************************************************************************ok: [128.199.76.43]

TASK [install mysql-server] *****************************************************************************************ok: [128.199.76.43]

TASK [uninstall mysql-server 1.0] *****************************************************************************************ok: [128.199.76.43]

PLAY RECAP *****************************************************************************************128.199.76.43              : ok=3    changed=0    unreachable=0    failed=0
```

Dan kita sudah belajar melakukan provisioning dengan menggunakan Ansible!