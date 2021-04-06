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

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/229ac7aa0268c1b2f632ca659b40a2ba95de0741/screenshots/8.png)

Perintah berikut untuk melihat versi ceph yang ter-install

```
sudo ceph --version
```

![](https://github.com/jhodysetiawansekardono/ceph-cluster-octpus/blob/229ac7aa0268c1b2f632ca659b40a2ba95de0741/screenshots/9.png)

Setelah itu buat cluster beranggotakan node server

```
ceph-deploy new server1 server2 server3
```

