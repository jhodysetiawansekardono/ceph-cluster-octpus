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
