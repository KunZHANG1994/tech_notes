# Ubuntu linux command notes

#### Q: ubuntu 18.04 & win 10 双系统时间不统一问题

```
sudo apt-get install ntpdate # 安装ntpdate
sudo ntpdate time.windows.com # 矫正服务器时间
sudo hwclock --localtime --systohc #设置硬件时间为本地
reboot
```

#### Q: ubuntu 18.04 安装实时内核

1. **下载实时补丁以及对应内核并解压到桌面**
2. 

Kernel:https://mirrors.edge.kernel.org/pub/linux/kernel/

RT patch: https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/

以下操作以patch-5.4.74-rt41 & linux-5.4.74 为例



```
sudo apt-get install libncurses-dev
cd /path/to/linux-5.4.74
patch -p1 < ../patch-5.4.74-rt41.patch

```

```bash
make oldconfig
make -j6
sudo make modules_install -j6
sudo make install -j6

sudo addgroup realtime
sudo usermod -a -G realtime $(whoami)

 dpkg --get-selections|grep linux

Afterwards, add the following limits to the realtime group in /etc/security/limits.conf:

@realtime soft rtprio 99
@realtime soft priority 99
@realtime soft memlock 102400
@realtime hard rtprio 99
@realtime hard priority 99
@realtime hard memlock 102400


sudo apt-mark hold linux-image-generic linux-headers-generic
sudo apt-mark unhold linux-image-generic linux-headers-generic
```

Reference:

https://blog.csdn.net/x356982611/article/details/77370385