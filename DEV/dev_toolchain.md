## Multi version tool management

```shell
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 10
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 11

sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 9
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-10 10
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 11

sudo update-alternatives --config gcc
sudo update-alternatives --config g++
```

## ld cache flush

```shell
sudo rm /etc/ld.so.cache
sudo ldconfig
```

## llvm

```shell
wget https://apt.llvm.org/llvm.sh
chmod +x llvm.sh
sudo ./llvm.sh <version number> # 17 or 18 is supported 2024.05.16
```

apt warns:

```shell
# modify /etc/apt/sources.list.d/archive_uri-http_apt_llvm_org_noble_-noble.list as bellow:
deb [arch=amd64] http://apt.llvm.org/noble/ llvm-toolchain-noble-18 main
deb-src [arch=amd64] http://apt.llvm.org/noble/ llvm-toolchain-noble-18 main
```

## cmake

~~**APT SOURCE 2024.05.16 NOT SUPPORTED**~~

2024.08.05 https://apt.kitware.com/ supported

TL;DR

```shell
test -f /usr/share/doc/kitware-archive-keyring/copyright ||
wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ noble main' | sudo tee /etc/apt/sources.list.d/kitware.list >/dev/null

sudo apt-get update

sudo apt-get install kitware-archive-keyring
```

## dbg-sym

2024.08.05
Package from noble-security suite can not be found here but at launchpad.

Someone has found this question but no response.

https://bugs.launchpad.net/ubuntu/+source/openssh/+bug/2075996

As described in Ubuntu doc, it's recommend to not use ddebs but debuginfod.

https://ubuntu.com/server/docs/about-debuginfod

debuginfod also use indexes from ddebs.ubuntu.com.

```shell
sudo apt install ubuntu-dbgsym-keyring

cat << 'EOF' | sudo tee -a /etc/apt/sources.list.d/ddebs.list
deb http://ddebs.ubuntu.com $(lsb_release -cs) main restricted universe multiverse
deb http://ddebs.ubuntu.com $(lsb_release -cs)-updates main restricted universe multiverse
EOF

```