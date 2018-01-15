---
layout: post
title: "Selfie Commit"
author: "dedenf"
tags:
    - jenius
    - scm
    - git
    - commits

categories: 
    - post
    
published: true
---

Sebagai _developer_ tentu sudah terbiasa dengan _Source Code Management_ seperti GIT, Mercurial, Bazaar, dan lainnya. pernah menggabungkan _Selfie_ atau swafoto berbarengan dengan dengan commit kode kita? di sini kita akan fokus dengan GIT sebagai _SCM_. 

Langkah-langkah yang ditulis dilakukan di komputer berbasis Mac, belum dicoba di Linux atau Windows.

#### Kebutuhan
Untuk menginstall paket-paket yang bisa dijalankan di _command line_ kita membutuhkan manajemen paket yang bernama [`homebrew`](https://brew.sh/)

#### install `brew`
Jalankan perintah ini   

`$ curl -fsS 'https://raw.githubusercontent.com/Homebrew/install/master/install' | ruby`

Perintah di atas akan menginstall beberapa _library_ tambahan untuk macOS.

#### Install paket
Setelah selesai proses instalasi `homebrew` dilanjutkan dengan instalasi paket berikut   
`$ brew install ffmpeg`   

Opsional   
`$ brew install imagemagick`     
`$ brew install tlassemble`

Setelah selesai semua, `cd` ke folder project GIT, lihat apakah ada file `post-commit` di folder `.git/hooks/`, kalo gak ada bisa ditambahkan melalui `$ vim .git/hooks/post-commit`, tambahkan line berikut (jangan lupa `:set paste` + `i`)

```
#!/bin/bash

### start post commit ###
if ! command -v ls .gitsnap >/dev/null; then
  printf "creating selfie folder...\n"
  mkdir ~/.gitsnap
fi

FILENAME=''
if command -v git rev-parse HEAD  > /dev/null; then
    FILENAME=$(date +%Y-%m-%d-%H-%M-%s)_FIRST
else
    FILENAME=$(date +%Y-%m-%d-%H-%M-%S)_$(git rev-parse HEAD)
fi
### take a selfie!
printf "📸  trying to take your awesome selfie..... 📸\n"
ffmpeg -f avfoundation -framerate 30 -i "0" -vframes 1 ~/.gitsnap/$FILENAME.jpg -loglevel quiet >> /dev/null
```
Line di atas akan membuat folder `.gitsnap` di `homedir` (atau bisa manual membuat folder sendiri di `home` anda)

#### Selfie!
Dengan menambahkan line tersebut di `hooks post-commit`, line tersebut akan dijalankan setiap kali melakukan `commit` dan `push` ke _repo_ git project, dan menyimpan foto _selfie_ di folder `.gitsnap`, nama _file_ foto yang diambil dengan fortmat **_TAN-GG-AL_commitid.jpg_**.

Untuk menyimpan selfie berdasarkan project, buat folder `.gitsnap` di masing-masing folder project yang sedang ada kerjakan.

#### Bonus   
Kita bisa membuat video dari file foto _selfie_ tersebut, kalau sudah banyak tentunya dengan perintah berikut  

`$ tlassemble ~/.gitsnap commit_timelapse.mov -fps 30 -codec h264 -quality high`

Dengan ini akan tercipta _file_ .mov yang bisa dimainkan atau di-share ke media sosial.

!TODO: insert recording video, colek [@marchell](/2017/10/18/engineer-marchell-immanuel/)
