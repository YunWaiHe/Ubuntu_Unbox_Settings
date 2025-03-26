## 窗口无边框

[Bug 2064177](https://bugs.launchpad.net/ubuntu/+source/gtk+3.0/+bug/2064177)

`crontab -e`添加：

```
@reboot $(sleep 30 && pkill -HUP mutter-x11-fram)
```

Wait for fix...  

## sshd socket-based activation

https://discourse.ubuntu.com/t/sshd-now-uses-socket-based-activation-ubuntu-22-10-and-later/30189/2

https://bugs.launchpad.net/ubuntu/noble/+source/openssh/+bug/2076023

still not fixed entirely

**It's highly recommended to disable sshd socket-based activation.**

```shell
# backup before rm
sudo rm -f /etc/systemd/system/ssh.service.d/*
sudo rm -f /etc/systemd/system/ssh.socket.d/*
# disable sshd socket-based activation
sudo  mkdir -p /etc/systemd/system-generators/
sudo  ln -s /dev/null /etc/systemd/system-generators/sshd-socket-generator
sudo  systemctl daemon-reload
sudo  systemctl disable --now ssh.socket
sudo  systemctl enable --now ssh.service
```





