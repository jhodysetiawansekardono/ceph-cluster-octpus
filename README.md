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
