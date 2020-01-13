---
layout: post
title: "Menginstal versi paket yang spesifik homebrew di macOS (contoh Tmux)"
author: "dedenf"
tags:
  - development
  - coding

categories: 
    - development

canonical: https://jakartadev.org/instal-paket-homebrew-versi-yang-spesifik/
published: true
---

Terkadang dalam melakukan pekerjaan, kita membutuhkan versi software yang spesifik, bisa karena alasan software yang kita bangun atau library yang kita pakai, hanya kompatibel dengan versi tertentu saja, ini bisa jadi masalah, saya terkena masalah ini ketika menggunakan sass, imageoptim, tmux.

Kita ambil contoh [Tmux](https://github.com/tmux/tmux/wiki), yang merupakan aplikasi multiplexter, sederhananya, tmux bisa mengemulasi terminal menjadi banyak dalam satu window, Tmux ini saya anggap sebagai improvement dari Screen.

Pada versi 3.0, Tmux versi ini banyak nge-_break_ konfigurasi dotfiles yang sudah saya buat, dan versi yang kompatible adalah versi sebelumnya, dan yang paling stabil adalah 2.8.

Menggunakan macOS, dan [Homebrew](https://brew.sh/) adalah manajemen paket yang selalu saya gunakan, untuk menginstall versi yang spesifik dari Tmux di Homebrew, terutama yang sudah tidak ditampilkan di formulanya, jika software tersebut masih terdapat daftar versinya jika kita lakukan perintah ini,

```shell
$ brew search node@

dedenf@elementium ~ $ brew search node@
==> Formulae
node ✔              node-sass           node@10          node@12
...
```

Untuk install versi yang lain di paket Homebrew tinggal install dengan perintah `$ brew install node@12` maka akan menginstal spesifik versi 12.

Jika versi yang dimaksud tidak ada, seperti Tmux, maka yang harus dilakukan adalah install melalui formula Tmux, caranya dengan meng-clone repo git Homebrew-core,

```shell
$ git clone git@github.com:Homebrew/homebrew-core.git
```

kemudian cari versi yang diinginkan, dengan mencari commit hash versi yang dimaksud, karena saya mencari versi 2.8, maka tinggal mencari commit yang mengandung versi tersebut.

```shell
$ git log -- Formula/Tmux.rb
```

Hasil 
```shell
...
commit 9d2ab46c92cc88470b1c54f5da2d0a1595188533
Author: Chongyu Zhu <i@lembacon.com>
Date:   Thu Oct 18 02:58:21 2018 +0800

    tmux 2.8

    Closes #33090.

    Signed-off-by: Chongyu Zhu <i@lembacon.com>
...
```

Dengan informasi di atas, kita bisa melakukan instalasi berdasarkan commit hash dari versi 2.8.

```shell
$ brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/9d2ab46c92cc88470b1c54f5da2d0a1595188533/Formula/tmux.rb
```

Viola! maka Tmux yang terinstall adalah versi 2.8! check versinya untuk lebih yakin,

```shell
$ tmux -V
tmux 2.8
```

Ini bisa diaplikasikan ke paket-paket lain yang sekiranya dibutuhkan, kadang versi terbaru tidak selalu dibutuhkan.