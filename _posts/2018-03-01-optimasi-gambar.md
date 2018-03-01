---
layout: post
title: "Optimasi gambar"
author: "dedenf"
tags:
    - image
    - optimization

categories: 
    - post
published: true
---

Sebagai developer, terutama web developer, load time dari satu halaman adalah penting, karena ini bisa dijadikan acuan apakah user akan tetap di web kita atau pergi karena load time website kita yang lama.

Dan hal ini tidak terbatas hanya pada web, pada aplikasi *mobile* juga optimasi gambar misalkan berupa assets yang cenderung berubah, atau misalkan jika aplikasi mengandung gambar profil cashtag nasabah Jenius, maka akan ada gambar yang keluar jika kita menambahkan user tersebut kedalam Jenius Contact.

Beberapa cara yang digunakan untuk mengurangi load time antara lain dengan browser cache, konfigurasi di webserver, mungkin juga menggunakan proxy cache, dan lainnya, dari banyak hal yang bisa dilakukan salah satunya adalah optimasi gambar, karena file gambar bisa sangat menyita waktu untuk loading, apalagi jika website anda mengandalkan media gambar sebagai alat komunikasi, misalkan e-commerce.

Beberapa pilihan alat untuk mengompress gambar, bisa untuk [desktop](http://mashable.com/2013/10/29/image-compressors) maupun CLI, untuk command line, saya menggunakan `jpegoptim` dan `pngout` sebagai tools untuk mengompress file gambar.
<!-- more -->
DI sini menggunakan Ubuntu sebagai base dari sistem operasi server   
`$ sudo apt-get install jpegoptim pngout`   
menjalankan perintah `$ jpegoptim file.jpg `, hal ini akan mengompress file gambar dengan menghilangkan info-info yang tidak dibutuhkan seperti comment di exif, atau informasi lain yang menambah berat satu file.

Anda juga bisa menjalankan perintah `jpegoptim` agar bisa secara masif mengompress file dengan perintah 

`$ find . -type f -name "*.jpg" -exec jpegoptim -m90 --strip-all --all-progressive  {} \;` 

Untuk perintah di atas, itu akan mencari file `.jpg` sampai ke dalam folder secara recursive,  Opsi `.` di atas menunjukkan working directory yang sedang anda gunakan, jika hendak mengarahkan ke folder yang lain bisa ditulis `PATH`-nya, seperti perintah di bawah.

`$ find /path/ke/gambar/ -type f -name "*.jpg" -exec jpegoptim -m90 --strip-all --all-progressive  {} \;`

Opsi yang digunakan adalah `-m90` artinya menggunakan kualitas 90, `--strip-all` menghilangkan informasi exif yang tidak terpakai, dan `--all-progressive` opsi ini untuk menjadikan gambar itu progressive mode.   
Output:   
`/path/ke/file_gambar.jpg 100x150 24bit N JFIF  [OK] 2706 --> 2580 bytes (4.66%), optimized.`   

**note:** terkadang ada kasus dimana file anda akan hilang permission code-nya, dan tidak bisa diakses oleh borwser, ini bisa diakali dengan menambahkan perintah seperti berikut   

`find . -type f -name "*.jpg" -exec jpegoptim -m90 --strip-all --all-progressive  {} \; -exec chmod 677 {} \;`

dengan cara di atas, anda mengembalikan permission file agar bisa dibaca oleh webserver dan akhirnya ditampilkan ke browser.

