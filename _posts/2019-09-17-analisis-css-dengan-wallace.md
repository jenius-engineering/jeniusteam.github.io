---
layout: post
title: "Analisis CSS dengan Wallace CLI"
author: "dedenf"
tags:
    - dev
    - css

categories: 
    - development

published: true
---

[CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) (Cascading Style Sheet) sebagai "bahasa" untuk menjadikan halaman web menjadi lebih cantik, saat ini sudah dalan versi 3 (CSS3 ditulisnya), adalah standar yang didukung oleh peramban-peramban (browser) yang mana akan me-render element yang ada di HTML/XML sesuai dengan apa yang kita ingin tampilkan di layar atau media lainnya.

<!-- break -->
<!-- more -->

CSS dalam web moderen digunakan sebagai modul yang dipakai ketika halaman di*render*, dengan semakin banyaknya *tools* yang digunakan untuk membuat dan merawat kode CSS yang dibuat, kemudian lahir juga CSS *pre-processor* seperti [SASS](https://sass-lang.com/), [LESS](http://lesscss.org/), [Stylus](http://stylus-lang.com/) dan beberapa preprocessor lainnya, dengan tujuan hampir sama, yakni agar CSS lebih bisa modular, dinamis dan lebih ekspresif dibanding dengan menulis CSS biasa.

Dengan semakin kompleknya CSS saat ini, melakukan analisis terhadap CSS yang kita buat menjadi sangat penting, gunanya antara lain untuk melihat apakah ukuran CSS kita besar, atau ada properti yang dibuat tapi tidak digunakan oleh entiti HTML, hal ini dipandang sebagai 'pemborosan'.

Untuk melakukan analisis, bisa menggunakan [Wallace-CLI](https://github.com/bartveneman/wallace-cli) dari [Project Wallace](https://www.projectwallace.com/), dari hasil analisis ini bisa dilakukan pengembangan atau melakukan perbaikan dari CSS yang sudah ada agar lebih optimal dan *loading* halaman lebih cepat pada akhirnya.

#### Install
Pastikan sudah ada [Nodejs](https://nodejs.org/en/) terinstal di mesin yang mana sudah terdapat [npm](https://www.npmjs.com/) untuk *package manager*, atau kalau sudah terbiasa dengan [Yarn](https://yarnpkg.com/) juga bisa.

```shell 
$ npm install -g wallace-cli
``` 
untuk instal secara global, untuk instal lokal folder hilangkan opsi `-g` atau dengan 
```shell 
$ yarn add --global wallace-cli
```

#### Penggunaan
Karena kita menginstal dengan opsi global (`-g`), maka kita bisa memanggil *executable wallace* dimanapun

`$ wallace https://jenius.tech/`
[![](/images/posts/wallace-small.jpg)](/images/posts/wallace-large.png)

Untuk opsi lain silakan melihat-lihat apa yang disediakan 
```shell
$ wallace -h 
Pretty CSS analytics on the CLI

  Usage
  	$ wallace https://www.projectwallace.com

  Options
  	--format, -f Format pretty (default) or JSON
  	--verbose, -v Show verbose output

  Examples
  	$ wallace https://projectwallace.com
  	$ wallace 'body { color: red; }'
  	$ echo 'html { font-size: 16px; }' | wallace
  	$ wallace 'html {}' --format=json
  	$ cat style.css | wallace --verbose
```

Dari hasil analisis bisa menentukan langkah apa yang bisa dilakukan untuk meminimalkan ukuran, atau properti yang bisa dibuang, digunakan, dan aksi lainnya.