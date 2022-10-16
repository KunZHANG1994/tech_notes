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