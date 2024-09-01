# Ubuntu 24.04 unbox settings



# APT and Snap

## APT config

```shell
# source
cat <<'EOF' | sudo tee /etc/apt/sources.list.d/ubuntu.sources
Types: deb deb-src
URIs: https://mirrors.cernet.edu.cn/ubuntu
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb deb-src
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb deb-src
URIs: http://ftp.udx.icscoe.jp/Linux/ubuntu/
Suites: noble noble-updates noble-backports noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
EOF
```

apt proxy

```shell
cat << 'EOF' | sudo tee /etc/apt/apt.conf.d/99proxy.conf
Acquire::http::Proxy "http://127.0.0.1:10809";
Acquire::https::Proxy "http://127.0.0.1:10809";
Acquire::http::Proxy {
    mirrors.zju.edu.cn "DIRECT";
}
Acquire::https::Proxy {
    developer.download.nvidia.cn "DIRECT";
}
EOF
```

package install

```shell
sudo apt install gtkhash alacarte bleachbit unar progress needrestart
```

package purge

```shell
sudo apt purge transmission* gnome-snapshot* remmina* openvpn* 
sudo apt autopurge
```



## Snap App

```shell
# remove unused snap apps
sudo snap remove --purge firefox
sudo snap remove --purge thunderbird
sudo apt purge firefox* thunderbird*
# refresh or restore snap-store
sudo snap refresh snap-store --channel=latest/stable/ubuntu-24.04
# set retain count
sudo snap set system refresh.retain=2
#Removes old revisions of snaps
cd
cat << 'EOF' | tee ~/.local/bin/clean_snap_old.sh
#!/bin/bash
#Removes old revisions of snaps
#CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
LANG=en_US.UTF-8 snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        sudo snap remove "$snapname" --revision="$revision"
    done
EOF

chmod +x ~/.local/bin/clean_snap_old.sh
sudo ~/.local/bin/clean_snap_old.sh
# block snap package from apt
cat << 'EOF'| sudo tee /etc/apt/preferences.d/snap-apps-disable
Package: chromium* firefox* thunderbird*
Pin: version /.*snap.*/
Pin-Priority: -99
EOF
# validate block result
apt policy firefox chromium-browser thunderbird
```



# Network config

## Cloud-init

```shell
sudo apt purge cloud-init
rm -r /etc/cloud/ && rm -rf /var/lib/cloud/
sudo rm /etc/netplan/50-cloud-init.yaml
sudo chmod 600 /etc/netplan/*.yaml
sudo netplan apply
sudo systemctl restart NetworkManager
```

## Disable ipv6

```shell
cat <<'EOF' | sudo tee /etc/sysctl.d/20-ipv6_disable.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
EOF
sudo deb-systemd-invoke restart procps.service
```

## NTP

```shell
sudo mkdir /etc/systemd/timesyncd.conf.d
sudo touch /etc/systemd/timesyncd.conf.d/public_ntp.conf
cat << 'EOF' | sudo tee /etc/systemd/timesyncd.conf.d/public_ntp.conf
[Time]
NTP=ntp.ntsc.ac.cn
FallbackNTP=ntp6.aliyun.com
EOF
sudo systemctl restart systemd-timesyncd.service
sudo systemctl status systemd-timesyncd.service
```

## DNS

```shell
sudo sed -r -i.orig 's/#?DNSStubListener=yes/DNSStubListener=no/g' /etc/systemd/resolved.conf
sudo systemctl restart systemd-resolved
resolvectl flush-caches
```



# Disk

## NTFS Auto Mount

```shell
sudo apt install ntfs-3g
sudo fdisk -l # 确定要挂载的磁盘分区
sudo blkid # 查看分区对应UUID TYPE
id # 查看uid gid
sudo mkdir /mnt/win_d_600G
sudo mkdir /mnt/win_e_1D4T
# 之后在/etc/fstab 中新加下面这一行，按实际情况修改，UUID改为对应的，挂载点选择/mnt/dir_name，类型ntfs-3g便于设置权限，后面的options意思是用户可以挂载，win文件夹名称合法性检查，属于当前用户，文件夹755，文件644，不备份，在根文件系统之后检查
cat << 'EOF' | sudo tee -a /etc/fstab
# win10 D: E:
UUID=E892549DFD9D9562 /mnt/win_d_600G ntfs-3g user,exec,dev,suid,windows_names,uid=1000,gid=1000,dmask=0022,fmask=0133 0 2
UUID=4A71C1335FE7A391 /mnt/win_e_1D4T ntfs-3g user,exec,dev,suid,windows_names,uid=1000,gid=1000,dmask=0022,fmask=0133 0 2
# HDD
UUID=bc6d7035-eec7-45bc-af48-d8d927cddb26 /mnt/HDD3D6T ext4 defaults 0 2
EOF

sudo systemctl daemon-reload
sudo mount -a
```




# Input Method

```shell
# remove unneed
sudo apt purge ibus* fcitx*
sudo apt autopurge
sudo rm -r ~/.cache/fcitx*
sudo rm -r ~/.config/fcitx*
sudo rm -r ~/.local/share/fcitx*
sudo rm -r ~/.cache/ibus*
sudo rm -r ~/.config/ibus*
sudo rm -r ~/.local/share/ibus*
sudo rm -r /root/.config/ibus
sudo rm -r /var/lib/gdm3/.config/ibus

sudo cat << 'EOF' | sudo tee /etc/environment.d/90fcitx.conf
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS="@im=fcitx"
EOF
sudo apt install fcitx libqt5qml5 libqt5quick5 libqt5quickwidgets5 qml-module-qtquick2 libgsettings-qt1
sudo cp /usr/share/applications/fcitx.desktop /etc/xdg/autostart/
```



# GNOME Tweak

## Tweaks

```shell
sudo apt install gnome-tweaks chrome-gnome-shell gnome-shell-extension-prefs gnome-shell-extension-manager
# manual install instead
# sudo apt install gdm-settings
# 扩展省略
# git clone https://github.com/PRATAP-KUMAR/gdm-extension/
# cd gdm-extension
# sudo make install
```

gdm-settings 删除后残留问题，

```shell
# 删除local gschemas
sudo rm /usr/local/share/glib-2.0/schemas/gschemas.compiled
# 重新生成
sudo glib-compile-schemas /usr/share/glib-2.0/schemas
# 打开dconf-editor验证
```

可能的原因：dconf-editor读取时，存在优先级，而gdm-settings在local创建了gschemas，卸载gdm-settings时没有删除掉这项内容，导致donf-editor读取的旧的数据。



# Software config

## Vim

```shell
sudo apt install vim
# vim config
cat << "EOF" | sudo tee -a /etc/vim/vimrc
set tabstop=4
set softtabstop=4
set shiftwidth=4
"set expandtab
"set smarttab
set autoindent
"set encoding=utf-8

call plug#begin()
Plug 'tpope/vim-sensible'
Plug 'junegunn/seoul256.vim'
Plug 'vim-scripts/httplog'
Plug 'tpope/vim-sleuth'
Plug 'ycm-core/youcompleteme'
call plug#end()

let g:ycm_global_ycm_extra_conf = '/usr/share/vim/plugged/youcompleteme/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py'
let g:ycm_key_list_select_completion = ['<TAB>']
let g:fencview_autodetect = 1

EOF

# main user
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
cd ~/.vim/plugged/youcompleteme
./install.py --clang-completer --ninja --verbose

# root user
sudo -i
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
cd ~/.vim/plugged/youcompleteme
./install.py --force-sudo --clang-completer --ninja --verbose
```



# Bugs

## nvidia unknow display

https://bugs.launchpad.net/ubuntu/+source/linux/+bug/2060268

```shell
cat <<'EOF' |sudo tee -a /usr/lib/udev/rules.d/71-nvidia.rules
ACTION=="add", SUBSYSTEM=="module", KERNEL=="nvidia_drm", TEST=="/sys/devices/platform/simple-framebuffer.0/drm/card0", RUN+="/bin/rm /dev/dri/card0"
EOF
```



# Other

## password

```shell
sudo su
sudo passwd
```

## disable wayland

```shell
sudo sed -i -e 's/^#WaylandEnable=false/WaylandEnable=false/' /etc/gdm3/custom.conf
```

## dpkg-reconfig

```shell
sudo dpkg-reconfigure tzdata
sudo dpkg-reconfigure ca-certificates
sudo dpkg-reconfigure console-setup
```

## locales

```shell
sudo dpkg-reconfigure locales
echo "zh_CN.UTF-8 UTF-8" | sudo tee /var/lib/locales/supported.d/zh-hans
echo "en_US.UTF-8 UTF-8" | sudo tee /var/lib/locales/supported.d/en
sudo locale-gen

# force english dir name if u choose Chinese while installing
# if needed, prepare the folder
#ln -s /mnt/mydisk/Folder/Documents/ /home/user1/Documents
#ln -s /mnt/mydisk/Folder/Music/ /home/user1/Music
#ln -s /mnt/mydisk/Folder/Pictures/ /home/user1/Pictures
#ln -s /mnt/mydisk/Folder/Videos/ /home/user1/Videos
LC_ALL=C xdg-user-dirs-update --force && sudo reboot
```

## HWE Kernel

```shell
sudo apt install --install-recommends linux-generic-hwe-24.04 linux-tools-generic-hwe-24.04
```

## AppImages

AppImages包依赖

```shell
sudo apt install libfuse2t64
```

## apparmor (fixed)

<font color=red>FIXED, 无需配置</font>

解决包括但不限于微信没法用、桌面缩略图不显示等诸多问题

https://bugs.launchpad.net/ubuntu/+source/nautilus/+bug/2047256

https://bugs.launchpad.net/ubuntu/+source/nautilus/+bug/2054183

```shell
cat << 'EOF' | sudo tee /etc/sysctl.d/90-apparmor.conf
kernel.apparmor_restrict_unprivileged_userns=0
EOF
sudo sysctl -p
sudo deb-systemd-invoke restart procps.service
```
