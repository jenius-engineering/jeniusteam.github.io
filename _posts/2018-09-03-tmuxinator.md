---
layout: post
title: "Tmuxinator"
author: "dedenf"
tags:
    - "tools"
    - unix
    - mac
    - linux
    - ruby

categories: 
    - article
    - howto

published: true
---

Di Jenius, _automation_ adalah salah satu kunci untuk men-*deliver* software dengan cepat, tanpa mengorbankan kualitas, automasi juga digunakan untuk kebutuhan sehari-hari, salah satunya untuk menyiapkan _environment_ development, Setelah sebelumnya diperkenalkan dengan [`tmux`](https://jenius.tech/2018/01/30/pengenalan-tmux/), yang sangat berguna untuk bekerja di _multi-shell_. di sini akan diperkenalkan _tool_ pendukung untuk tmux, yaitu [`tmuxinator`](https://github.com/tmuxinator/tmuxinator)[^1], tool ini digunakan untuk meng-automasi shell yang biasa digunakan untuk developer, biasanya untuk mempersiapkan shell yang digunakan.

<!-- more -->

Sebagai contoh, saya menggunakan 6 'tab' di shell yang saya gunakan.

![shell](/images/posts/tmuxinator/tmuxinator.jpg)

Jika kita melakukan pembukaan shell secara manual, misalkan jika `keybinding` menggunakan `ctrl + a + c` maka akan dibutuhkan setidaknya 6 kali untuk membuat 6 tab. kita bisa menggunakan Tmuxinator untuk meng-automasi ini, menjadikan tiap kali kita membuka tmux, shell yang menjadi favorit atau shell yang biasa kita gunakan untuk bekerja bisa langsung terhadirkan.

### install
sesuai dengan howto dari github page-nya, kita install tmuxinator, dan karena tmuxinator dipaketkan sebagai `gem`, maka untuk meng-install menginstallnya digunakan `ruby-gem`.

`gem install tmuxinator` ↩

Setelah ini, set editor yang anda gunakan, saya menggunakan `vim` sebagai default editor, jika hendak mengubahnya, tinggal ubah shell config ke editor pilihan, misalkan jika hendak mengubah default editor ke vim atau visual code editor dengan perintah `export EDITOR='vim'` atau `export EDITOR='code'` untuk vsc.

Setelah setting default editor buatlah project baru dengan perintah `tmuxinator new development` (di sini saya menggunakan development sebagai nama project), dan akan diberikan semacam template window yang bisa digunakan sebagai tahap awal.

Untuk setting saya sendiri, berikut file yml tmuxinator project yang dibuat.

```
# ~/.tmuxinator/development.yml

name: dev
root: ~/

windows:
  - System:
      layout: even-vertical
      root: ~
      panes:
        - htop
        - vtop
  - JeniusApp: 
      layout: even-vertical
      root: ~/Developments/jenius/jenius2-apps/
      panes:
        - gss
        - git pull origin develop

  - Microservice:
      layout: even-vertical
      root: ~/Developments/jenius/microservice/
      panes:
        - git multi pull

  - Mongo:
      layout: tiled
      panes:
        - cd ~/Developments/devops/mongodb-vagrant/ && vagrant status
        - cd ~/Developments/devops/mongodb-vagrant/ && vagrant status
        - cd ~/Developments/devops/mongodb-vagrant/ && vagrant status
        - cd ~/Developments/devops/mongodb-vagrant/ && vagrant status

  - Orchestrator:
      layout: tiled
      panes:
        - ls
  - Side:
      layout: tiled
      root: ~/Developments/blogs/
      panes:
        - cd ~/Developments/blogs/dedenf.github.io; gss
        - cd ~/Developments/blogs/jeniusteam.github.io; gss
        - cd ~/Developments/blogs/jakartadev.github.io; gss
        - cd ~/Developments/blogs/epicoffee.github.io; gss
  
  - Infra:
      layout: tiled
      panes:
        - ls
```

Untuk koleksi dotfiles yang digunakan, saya simpan [di repo ini](https://github.com/dedenf/dotfiles)

### Eksekusi
Setelah membuat konfigurasi yang dibutuhkan, jalankan dengan menggunakan perintah 

`$ tmux` ↩

Dengan memanggil perintah ini, maka session tmux dibuat, baru kita bisa menjalankan `tmuxinator` diatas session `tmux`.

`$ tmuxinator development` ↩

Maka shell anda akan menampilkan 6 tab, sesuai dengan konfigurasi yang ditulis. Silakan ubah dan gunakan perintah sesuai dengan project yang sedang aktif digunakan.



### Footnote

[^1]:tmuxinator salah satu tool yang bisa digunakan, masih banyak project lain yang bisa digunakan, misalkan [teamocil](https://github.com/remiprev/teamocil) dan project lainnya.