---
layout: post
title: "Editorial, developer's way"
author: dedenf
tags:
    - "how to"
    - jenius
published: true
---

Dalam penulisan satu artikel selalu terdapat setidaknya penulis dan editor yang akan me-*review* tulisan yang dibuat oleh penulis, artikel yang ditulis bisa berupa artikel, _how to_ dan lainnya. Kalau dirunut, hal ini mirip dengan yang dilakukan di Jenius sendiri di kalangan _developer_, yaitu _code review_, dimana _developer_ _commit_ kode mereka ke _source code management_ (bisa Github, Gitlab, Bitbucket) sesuai dengan _branch_ mereka masing-masing, kemudian melakukan _pull request_ terhadap _techlead_ atau orang yang ditunjuk untuk melakukan _review_ tersebut.

<!-- more -->

Hal ini tentu bisa diimplementasikan dengan proses _editorial_, Jenius.tech melakukan proses _editorial_ ini dengan menggunakan cara [git-flow](https://github.com/nvie/gitflow) (tidak 100% tentunya), artikel yang _live_ di jenius.tech adalah bentuk dari branch _master_, dan _branch_ _develop_ adalah _branch_ yang digunakan untuk pengumpulan artikel yang ditulis dari branch _post_, kurang lebih alurnya sebagai berikut:

![article flows](/images/posts/article-published.jpg)

Semua artikel yang dibuat diblog ini disimpan di *repository* [Jenius Team](https://github.com/jeniusTeam/jeniusteam.github.io), dengan menggunakan Jekyll sebagai mesin blog-nya, dirasa sangat fleksibel dan juga memenuhi kebutuhan dalam mem-*publish* artikel.
