# DEPLOY CEPH OCTOPUS CLUSTER PADA UBUNTU BIONIC BEAVER (18.04 LTS) DENGAN MENGGUNAKAN CEPH-DEPLOY

## CEPH-DEPLOY SETUP

Saya akan men-deploy ceph menggunakan vm deployer sesuai topology dibawah ini:

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/c7b5242bd29d78fee52510cc7e58686553e65d35/resources/topology.jpg)


Pertama-tama saya akan menginstalasi ceph-deploy pada vm deployer dengan command berikut

```
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
echo deb https://download.ceph.com/debian-octopus/ $(lsb_release -sc) main | sudo tee /etc/apt/sources.list.d/ceph.list
sudo apt update
sudo apt install ceph-deploy
```
Kemudian untuk semua vm install packet pendukung karena ceph-deploy memerlukannya supaya bisa berjalan dengan baik

```
sudo apt install ca-certificates apt-transport-https python-minimal
```

Jangan lupa edit /etc/hosts supaya antar vm bisa terhubung dengan menggunakan host-nya masing-masing

```
127.0.0.1 localhost
10.10.10.9 deployer
10.10.10.11 server1
10.10.10.12 server2
10.10.10.13 server3
```

Supaya lebih praktis tambahkan konfigurasi sudo tanpa password di setiap node dengan perintah ini

```
echo "student ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/student
sudo chmod 0440 /etc/sudoers.d/student
```

Buat juga ssh dengan menggunakan otentikasi kunci kriptografi lalu bagikan ke node server

```
ssh-keygen
ssh-copy-id server1
ssh-copy-id server2
ssh-copy-id server3
```

## MEMBUAT CLUSTER

Pada node deployer buatlah sebuah directory yang akan menampung file konfigurasi dengan perintah berikut

```
mkdir my-cluster
cd my-cluster
```

Selanjutnya saya tinggal menginstalasi ceph octopus pada node deployer dengan perintah berikut lalu tunggu sampai selesai

```
ceph-deploy install --release octopus server1 server2 server3
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/08.png)


Perintah berikut untuk melihat versi ceph yang ter-install

```
sudo ceph --version
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/09.png)


Setelah itu buat cluster beranggotakan node server

```
ceph-deploy new server1 server2 server3
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/10.png)


Pada ceph.conf konfigurasi yang sudah dilakukan tadi akan diletakan pada directory dimana perintah dijalankan


![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/11.png)

Selanjutnya deploy mon initial dengan perintah

```
ceph-deploy mon create-initial
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/12.png)


Berikut adalah outputnya

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/13.png)


Copy file konfigurasi admin key ke node server

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/14.png)


Deploy ceph manager pada node server1

```
ceph-deploy mgr create server1
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/15.png)


Kemudian deploy ceph object storage daemon pada node server

```
ceph-deploy osd create --data /dev/vdb server1
ceph-deploy osd create --data /dev/vdc server1
ceph-deploy osd create --data /dev/vdd server1
ceph-deploy osd create --data /dev/vdb server2
ceph-deploy osd create --data /dev/vdc server2
ceph-deploy osd create --data /dev/vdd server2
ceph-deploy osd create --data /dev/vdb server3
ceph-deploy osd create --data /dev/vdc server3
ceph-deploy osd create --data /dev/vdd server3
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/16.png)


Gunakan perintah ini untuk mengecek cluster health

```
ssh server1 sudo ceph health
ssh server1 sudo ceph -s
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/17.png)


Untuk high-avability saya akan menambahkan node server2 dan server3 untuk menjadi ceph monitor

```
ceph-deploy mon add server2
ceph-deploy mon add server3
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/18.png)


Gunakan perintah ini untuk mengecek status quorum

```
ssh server1 sudo ceph quorum_status --format json-pretty
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/19.png)


Deploy juga ceph manager pada node server2 dan node server3 untuk high avability

```
ceph-deploy mgr create server2 server3
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/20.png)


Selanjutnya gunakan perintah berikut untuk melihat standby manager

```
ssh server1 sudo ceph -s
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/5068d37c87820416ce70ae5ca39f17f64c00ede5/screenshots/21.png)
