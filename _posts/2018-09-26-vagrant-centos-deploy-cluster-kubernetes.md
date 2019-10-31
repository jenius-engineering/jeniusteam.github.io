---
layout: post
title: "Membangun cluster Kubernetes di Vagrant dan CentOS"
author: "dedenf"
tags:
    - kubernetes
    - unix
    - devops

categories: 
    - article
    - howto
    - kubernetes
    - devops

published: true
canonical: https://jakartadev.org/deploy-kubernetes-cluster-vagrant-centos/
---


[Kubernetes](https://kubernetes.io/), sebagai sistem orkestrasi container yang dirilis open-source untuk mengautomasi deployment, dan juga dapat digunakan untuk mengatur aplikasi di server, saat ini sedang naik daun, dan banyak digunakan di beberapa perusahaan besar, saat ini penyedia layanan cloud seperti Amazon (AWS EKS), Google (GKE), AliCloud dan beberapa penyedia lain juga menyediakan layanan Kubernetes.

Di sini akan mencoba membangun cluster kubernetes dilokal mesin kita, bisa berupa laptop atau desktop yang biasa digunakan, untuk membangun cluster ini setidaknya dibutuhkan beberapa hal berikut:

* RAM 8GB 
* Storage, setidaknya membutuhkan 4GB per-node
* Virtualbox
* Vagrant (opsional)

<!-- more -->

Vagrant digunakan sebagai alat untuk membuat development environment lebih mudah disini, dan virtualbox digunakan sebagai mesin virtualisasinya, cluster ini terdiri dari 1 `master` dan 3 `node` (slave) kubernetes. Untuk OS yang digunakan adalah GNU/Linux distribusi Centos, bisa saja menggunakan distro lain, seperti Ubuntu, tinggal ubah perintah package management sesuai dengan distro yang digunakan.

### Setup
Untuk setup awal, kita akan mendefinisikan host atau node yang akan kita gunakan di Vagrantfile, disini akan disetup 4 node, sourcecode untuk artikel ini ada [disini](https://github.com/dedenf/kubernetes).

`$ vagrant up`

Untuk menghidupkan 4 node yang sudah ditulis di `Vagrantfile`. Setelah semua node hidup, kita bisa melihat status dari masing-masing node di vagrant dengan perintah `$ vagrant status` dan akan mendapat respon berikut.    

```bash
Current machine states:

master                    running (virtualbox)
kube1                     running (virtualbox)
kube2                     running (virtualbox)
kube3                     running (virtualbox)
```

Setelah hidup *node-node* tersebut, ssh ke node master `$ vagrant ssh master`.  

```bash
[vagrant@master ~]$ sudo su - 
```

Perintah ini untuk bisa bertukar posisi ke `root` sehingga lebih mudah, untuk yang lebih *concern* dengan keamanan, cukup dengan perintah `sudo` ditiap perintah yang membutuhkan privilege lebih tinggi, hanya pastikan bahwa user anda bisa sudah mendapatkan izin di `visudo`.

### Mempersiapkan host
#### Mendaftarkan IP Address
Agar masing-masing hosts dapat mengenali satu sama lain, baik master dan worker, dibutuhkan untuk mendaftarkan masing-masing IP address di setiap node (master node dan worker node), lakukan edit di file `/etc/hosts`, dan tambahkan IP-nya, untuk setup workshop ini, seperti yang dituliskan di `Vagrantfile`, digunakan daftar IP sebagai berikut

```bash
master  192.168.10.11
kube1   192.168.10.12
kube2   192.168.10.13
kube3   192.168.10.14
```

_hal ini bisa saja diautomasi misalkan dengan Ansible atau provisioning di Vagrant_

#### Firewall dan filesystem
setelah kita akan mendaftarkan 4 node yang digunakan ini di `/etc/hosts` masing-masing node, selanjutnya adalah melakukan perubahan setting di firewall dan filesystem agar masing-masing node bisa berkomunikasi tanpa ada pengecekan paket dan akses ke node-node yang ada di cluster.

```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo setenforce 0 
[vagrant@master ~]$ sudo sed -i --follow-symlinks \ 
                    's/SELINUX=enforcing/SELINUX=disabled/g' \
                    /etc/sysconfig/selinux
```

Disable swap
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo swapoff -a
```

Dan edit file di `/etc/fstab`
```shell
# lakukan ini di semua node
[vagrant@master ~]$ sudo vi /etc/fstab
```
cari dan ubah baris ini dengan menambahkan komentar
```shell
/dev/mapper/VolGroup00-LogVol01 swap                    swap    defaults        0 0
```
Menjadi 
```shell
#/dev/mapper/VolGroup00-LogVol01 swap                    swap    defaults        0 0
```

Kemudian aktifkan kernel modul `br_netfilter`, hal ini agar paket yang berkomunikasi melalui `bridge` diproses oleh `iptables` untuk dilakukan filter paket asal dan juga untuk kebutuhan komunikasi antar `pods` didalam `cluster`.

```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo modprobe br_netfilter
[vagrant@master ~]$ sudo echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

#### Install Docker, Kubernetes dan paket lainnya
Kubernetes membutuhkan *container*, dan yang kita gunakan untuk workshop ini adalah Docker, untuk menginstall-nya ikuti langkah berikut

```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```


##### Tambahkan repo Docker
Tambahkan repo docker ke centos repo master dan workers
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

Di _howto_ setup kubernetes, disarankan untuk menggunakan menggunakan docker versi `17.03`, tapi sejauh ini menggunakan versi `18.06.1-ce` tidak ada masalah, karena jika install langsung biasanya merujuk ke versi yang terbaru, maka untuk memilih menggunakan versi yang diharapkan bisa menggunakan perintah
```bash
[vagrant@master ~]$ sudo yum list docker-ce --showduplicates | sort -r
```

Dan untuk install versi yang spesifik sesuai dengan tulisan versi yang keluar dari hasil menjalankan perintah di atas.
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo sudo yum install docker-ce-<VERSION STRING>
```

#### Install Kubernetes
Untuk menginstall kubernetes, tambahkan repo kubernetes ke centos repo di semua node.
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

Install `kubectl`, `kubeadm` dan `kubelet`
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo yum install -y kubelet kubeadm kubectl
```

Kemudian reboot semua node.

Setelah semua node up, enable service docker, kubelet di sytemd dan juga mulai service docker, kubelet

```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo systemctl start docker && systemctl enable docker
[vagrant@master ~]$ sudo systemctl start kubelet && systemctl enable kubelet
```

Cek apakah docker masuk Cgroup driver
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo docker info | grep -i cgroup
```

Masukkan cgroup-driver ini masuk ke konfigurasi `kubeadm` ke `cgroupfs`
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo sed -i 's/cgroup-driver=systemd/cgroup-driver=cgroupfs/g' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```

Reload systemd dan restart service kubelet
```bash
# lakukan ini di semua node
[vagrant@master ~]$ sudo systemctl daemon-reload
[vagrant@master ~]$ sudo systemctl restart kubelet
```

#### Inisiasi cluster
Langkah ini untuk menginisiasi cluster yang dimulai di `master` terlebih dahulu, karena dari `master` inilah kita dapat melakukan operasi yang berhubungan dengan cluster.

```bash
# lakukan HANYA di master
[vagrant@master ~]$ sudo kubeadm init --apiserver-advertise-address=192.168.10.11 --pod-network-cidr=10.244.0.0/16
```
Opsi perintah `--apiserver-advertise-address=` di atas untuk menginisiasi `master` sebagai apiserver dan disini master memiliki IP address `192.168.10.11`, dan `--pod-network-cidr=` adalah untuk menspesifikasi range IP yang digunakan nanti untuk pod-pod yang akan hidup dicluster. Untuk virtual networking atau network overlay, workshop ini menggunakan `flanel`, [selengkapnya bisa dilihat disini](https://kubernetes.io/docs/concepts/cluster-administration/networking/) untuk pilihan networking model lainnya selain `flanel`.

Setelah perintah di atas dijalankan, jika sukses, akan keluar pesan-pesan sukses dan fokus ke baris terakhir yang berisi baris ini, yang akan dijalankan dimasing-masing node worker. baris ini mengandung token dan juga perintah untuk join cluster.

Agar kubernetes bisa digunakan, maka diperlukan file konfigurasi yang akan kita simpan di `$HOME` user kita, tidak disarankan menjalankan kubernetes sebagai root. Untuk itu, kita siapkan direktori dotfile, dan juga salin file `admin.conf` di kubernetes folder.

```bash
# lakukan ini HANYA di master
[vagrant@master ~]$ mkdir -p $HOME/.kube
[vagrant@master ~]$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[vagrant@master ~]$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Seperti yang kita sebutkan sebelumnya, kita menggunakan `flannel`, untuk itu kita akan mendeploy `flannel` di master node kita.

```bash
[vagrant@master ~]$ sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
Perintah di atas akan mendeploy flannel networking cluster.

```bash
[vagrant@master ~]$ kubectl get nodes
[vagrant@master ~]$ kubectl get pods --all-namespaces
```
Output perintah tersebut
```bash
NAMESPACE     NAME                                 READY     STATUS     RESTARTS   AGE
kube-system   coredns-78fcdf6894-5kcl4             1/1       Running    7          19d
kube-system   coredns-78fcdf6894-dmckh             1/1       Running    6          19d
kube-system   etcd-master                          1/1       Running    7          19d
kube-system   kube-apiserver-master            1/1       Running    6          19d
kube-system   kube-controller-manager-master   1/1       Running    7          19d
kube-system   kube-flannel-ds-amd64-2smlw          1/1       Running    7          19d
kube-system   kube-proxy-47zxs                     1/1       Running    0          19d
kube-system   kube-scheduler-master            1/1       Running    7          19d
```
Bisa dilihat flannel untuk master telah up, dan pastikan `kube-system` berstatus RUNNING, dengan ini maka inisiasi master telah sukses.


#### Menambahkan node worker ke cluster
Langkah selanjutnya adalah menambahkan `kube1`, `kube2` dan `kube3` ke cluster, yang pertama dilakukan adalah perintah yang sebelumnya muncul dari hasil `kubeadmin init` diatas.

```bash
# lakukan ini di semua node WORKER
[vagrant@kube1 ~]$ sudo kubeadm join 192.168.10.11:6443 --token 0d3sfd.0ky3mcq2hof7a72a --discovery-token-ca-cert-hash sha256:9adc5abcb739cac1ff5fcf0b8d3523c869ed498af768eb85b5187e570869e949
```
Jalankan perintah diatas disemua node worker di cluster.Jika sudah, silakan cek apakah node worker sudah bergabung dengan cluster ini atau belum.

```bash
[vagrant@master ~]$ kubectl get nodes 
[vagrant@master ~]$ kubectl get pods --all-namespaces
```
akan menghasilkan tabel status seperti ini

```bash
NAME         STATUS     ROLES     AGE       VERSION
master       Ready      master    19d       v1.11.2
kube1        Ready      <none>    19d       v1.11.2
kube2        NotReady   <none>    19d       v1.11.2
kube3        NotReady   <none>    19d       v1.11.2
```

Tabel diatas mengindikasikan bahwa node worker dengan nama `kube1` sudah join cluster, lakukan perintah yang sama terhadap sisa node worker yang belum join.
Jika semua node telah berstatus `Ready`, maka cluster sudah selesai dikonfigurasi.

#### Testing cluster dengan deploy pod
Untuk mengetest apakah cluster sudah bisa digunakan untuk deployment dan menerima request, kita buat deploymet dan service sederhana, kita akan mendeploy webserver nginx dicluster ini.

Dimulai dengan membuat deployment dinode master.
```bash
[vagrant@master ~]$ kubectl describe deployment nginx
deployment.apps/nginx created
```
Cek apakah deployment ini sudah ada, dan juga untuk melihat spesifikasi pod
```shell
[vagrant@master ~]$ kubectl describe deployment nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Tue, 25 Sep 2018 16:51:45 +0000
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision=1
Selector:               app=nginx
Replicas:               1 desired | 1 updated | 1 total | 0 available | 1 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
...
```
Untuk dapat mengakses nginx ini, diperlukan untuk mengekspos nginx service dengan perintah 
```shell
[vagrant@master ~]$ kubectl create service nodeport nginx --tcp=80:80
service/nginx created
``` 
Lihat apakah pods nginx sudah up
```shell
[vagrant@master ~]$ kubectl get pods
NAME                     READY     STATUS    RESTARTS   AGE
nginx-78f5d695bd-29jzh   1/1       Running   0          8m
```
Dan lihat juga service yang sudah ter-ekspos

```shell
[vagrant@master ~]$ kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        19d
nginx        NodePort    10.96.12.181   <none>        80:31802/TCP   8m
```
Di sini nginx jalan di IP `10.96.12.181` didalam cluster dan dengan port 80, dan jika kita coba akses dari master node

```shell
[vagrant@master ~]$ curl 192.168.10.12:31802
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...

```

Via browser

[![kubernetes nginx](/public/images/posts/kubernetes-nginx.png)](/public/images/posts/kubernetes-nginx-large.png)



