# Ubuntu linux command notes

#### Q: ubuntu 18.04 & win 10 双系统时间不统一问题

```
sudo apt-get install ntpdate # 安装ntpdate
sudo ntpdate time.windows.com # 矫正服务器时间
sudo hwclock --localtime --systohc #设置硬件时间为本地
reboot
```

#### Q: ubuntu 20.04 & win 10 双系统时间不统一问题
```
timedatectl set-local-rtc 1 --adjust-system-clock
```

#### Q: ubuntu 20.04 给USB接口赋予权限（当出现无法读取外接设备时使用,此处以ttyUSB0为例）
```
dmesg | grep ttyUSB0 # 此处ttyUSB0为你的目标接口，也可以是其它
lsusb # 也可以用该指令查找确定自己需要的接口名称
sudo usermod -a -G dialout $USER #给当前用户加入权限组
sudo chmod 777 ttyUSB0 # 给目前接口赋予权限


```


