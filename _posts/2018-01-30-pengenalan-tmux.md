---
layout: post
title: "Pengenalan tmux"
author: "dedenf"
tags:
    - tmux
    - unix

categories: 
    - post
image: /images/posts/tmux.png
published: true
---

Sering menggunakan banyak window atau tab? misalkan membuka `vim`, `ssh` ke server, koneksi ke database server, `docker` di tab yang lain, dan akhirnya akan banyak tab atau _window_ yang dibuka. di Jenius khususnya dan sebagai developer pada umumnya, hal ini pasti terjadi hampir setiap hari.

Agar tidak perlu mengatur banyak window atau tab, hal ini bisa diatur oleh [`tmux`](https://tmux.github.io/) atau Terminal Multiplexer, sebuah *command line tool* yang berguna diantaranya untuk,
<!-- more --> 

1. Mengatur multiple window/pane dalam satu window terminal.
2. Mengatur session dalam terminal tersebut.
3. Mencegah session yang hilang karena tidak sengaja menutup aplikasi terminal.

dan kegunaan lainnya, untuk _sysadmin_ dan _developer_ hal ini akan sangat membantu.

#### Memulai
Untuk memulai menggunakan `tmux`, install terlebih dahulu aplikasinya melalui `brew`, `apt` atau package manager yang ada, `$ brew install tmux`. 

Setelah berhasil diinstall, jalankan perintah `tmux` di terminal

`$ tmux`

Di terminal akan muncul seperti ini (note: terminal ini menggunakan iTerm)
![tmux](/images/posts/tmux.png)

di sini session tmux server sudah jalan, dan kita bisa memanggil banyak tmux session yang akan hidup dalam server tersebut.
```
$ tmux list-sessions
0: 2 windows (created Tue Jan 30 11:28:59 2018) [133x38] (attached)
1: 1 windows (created Tue Jan 30 14:23:28 2018) [204x69]
```
Perintah di atas adalah untuk melihat session yang aktif di tmux saat ini, ini bisa menjadi banyak ketika kita membuka banyak session tmux, lakukan perintah `tmux` di window lain dan akan terdaftar jika kita melakukan `tmux list-sessions`.

#### Mengatur session
Untuk mengatur session di dalam tmux, ada perintah yang bisa digunakan, diantaranya:
- `<prefix> + c` membuka tab baru di session tmux yang sedang aktif
- `<prefix> + [0,1,etc]` untuk pindah dari tab satu ke tab lain, sesuai dengan sekuen-nya.
- `<prefix> + x` untuk menutup tab yang sedang aktif
- `<prefix> + d` untuk detach dari tmux session yang aktif, dan kembali ke normal _command line interface_.

`<prefix>` default yang dipakai oleh tmux adalah `ctrl+b`, `ctrl + c` misalkan untuk membuka tab baru.

Dan untuk mengatur session tmux sendiri salah satunya `list-sessions` seperti yang dibahas di atas, peritah lain antara lain:
- `tmux kill-session -t nomor-session` akan menutup session nomor x yang ditulis.
- `tmux attach-session -t nomor-session` atau `tmux a` akan memanggil session yang sebelumnya sudah di*detach* atau memanggil session tmux yang terakhir digunakan.

dan masih banyak perintah lainnya yang bisa di*explore* untuk mengatur tmux.

#### Kostumasi tmux
Tmux bisa dikostumasi sesuai dengan kebutuhan kita, yang biasanya banyak dikostumasi ada prefix yang digunakan, karena `ctrl+b` terkadang tidak kompatibel dengan vim, kita bisa mengganti dengan `ctrl+a`, juga untuk sekuen dari tab/window tmux yang default-nya dimulai di sekuen `0`, diubah menjadi `1` agar lebih gampang mengaturnya.

kita bisa memulai kostumasi di file konfigurasi tmux, yang terletak biasanya di home folder, jika tidak ada, bisa mulai dengan membuat file `.tmux.conf`.

`$ vim ~/.tmux.conf`

tambahkan baris berikut

```
# unbind ctrl+b, ubah ke ctrl+a
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# atur agar sekuen dimulai dari 1
set -g base-index 1
```

Hasil tampilan terminal dari hasil setting konfigurasi `.tmux.conf` di atas seperti di bawah.

![tmux new setting](/images/posts/tmux2.png)

source bisa dilihat di repo [dotfiles](https://github.com/jeniusTeam/dotfiles) Jenius Tech team (repo ini akan bertambah dengan dotfiles aplikasi lain).