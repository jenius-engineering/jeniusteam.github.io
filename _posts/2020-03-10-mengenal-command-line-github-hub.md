---
layout: post
title: "Mengenal hub dari Github"
author: "dedenf"
tags: 
    - software
    - development
categories: 
    - development

canonical: https://jakartadev.org/mengenal-hub-command-line-git-github/
publish: true
---

![](/images/posts/hub.jpg)

[Hub](https://hub.github.com/), yang merupakan satu program yang dirilis oleh Github agar kita bisa melakukan aktivitas di Github melalui command line.

Hal ini sangat berguna untuk developer atau orang yang memiliki tanggung jawab pada repo yang dibuat.

Hub sendiri merupakan ekstensi dari perintah `git`, yang artinya hub dibangun di atas perintah git yang sudah ada, beberapa perintah hub ini dibuat spesifik untuk berhubungan dengan github, seperti `pull-request` dan beberapa perintah lain yang spesifik Github juga, selain _subcommand_ ini, hub bisa digunakan juga untuk komunikasi dengan penyedia layanan git yang lain seperti [Bitbucket](https://bitbucket.org/) atau [Gitlab](https://gitlab.com/).

```shell
❯ hub help
usage: git [--version] [--help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]
...
These GitHub commands are provided by hub:

   api            Low-level GitHub API request interface
   browse         Open a GitHub page in the default browser
   ci-status      Show the status of GitHub checks for a commit
   compare        Open a compare page on GitHub
   create         Create this repository on GitHub and add GitHub as origin
   delete         Delete a repository on GitHub
   fork           Make a fork of a remote repository on GitHub and add as remote
   gist           Make a gist
   issue          List or create GitHub issues
   pr             List or checkout GitHub pull requests
   pull-request   Open a pull request on GitHub
   release        List or create GitHub releases
   sync           Fetch git objects from upstream and update branches
```



### Instalasi
Untuk pengguna mac, instalasi cukup gampang, dengan menggunakan `homebrew`, untuk yang belum mengetahui homebrew bisa membaca artikel [jenius.tech yang lalu](https://jenius.tech/2018/01/11/hipster-commit/).

```shell
> brew install hub
```
verifikasi instalasi dan versinya

```shell
❯ hub version
git version 2.25.1
hub version 2.14.2
```

### Penggunaan
Seperti penjelasan di atas, hub dapat digunakan untuk repository git yang ada di mesin kita, mengaturnya, seperti membuat branch, commit, push dan operasi git lainnya

```shell
❯ hub status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
```

Dan untuk perintah yang spesifik terhadap Github misalkan, bisa kita test dengan menggunakan subcommand `create` dan `delete` yang akan membuat repository baru dan menghapusnya.

```shell
❯ hub create microservice-cendol
A git remote named 'origin' already exists and is set to push to 'ssh://git@github.com/dedenf/dedenf.github.io.git'.
https://github.com/dedenf/microservice-cendol
```
Dengan perintah di atas, saya membuat repo `microservice-cendol`, dan jika kita lihat di halaman repo Github user kita

![](/images/posts/hub-create.jpg)

Atau jika kita sudah memiliki project di mesin kita yang hendak kita jadikan repo di Github, bisa dengan perintah git dulu untuk menginisiasi repo, dan kemudian membuat repo-nya berdasarkan folder yang sedang aktif.

```shell
~/Developments/tem/hub
❯ touch readme.md

~/Developments/tem/hub
❯ git init
Initialized empty Git repository in /Users/dedenf/Developments/tem/hub/.git/

~/Developments/tem/hub master*
❯ git add .

~/Developments/tem/hub master*
❯ git commit -m "init"
[master (root-commit) 7c38066] init
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 readme.md

~/Developments/tem/hub master
❯ hub create
Updating origin
https://github.com/dedenf/hub
```

Dan masih banyak lagi perintah yang bisa digunakan dalam pengoperasian git melalui perintah hub ini, silakan [baca-baca dokumentasinya](https://hub.github.com/hub.1.html).