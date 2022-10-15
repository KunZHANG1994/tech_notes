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
#### Q: ubuntu 20.04 安装实时内核

1. **下载实时补丁以及对应内核并解压到桌面**
2. 
choosing the version closest to the one you currently use. 

Kernel:https://mirrors.edge.kernel.org/pub/linux/kernel/

RT patch: https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/

For Ubuntu 20.04 tested with the kernel version 5.9.1:

```
curl -SLO https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.9.1.tar.xz
curl -SLO https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.9.1.tar.sign
curl -SLO https://www.kernel.org/pub/linux/kernel/projects/rt/5.9/patch-5.9.1-rt20.patch.xz
curl -SLO https://www.kernel.org/pub/linux/kernel/projects/rt/5.9/patch-5.9.1-rt20.patch.sign
```
Install the necessary dependencies:
```
sudo apt-get install build-essential bc curl ca-certificates gnupg2 libssl-dev lsb-release libelf-dev bison flex dwarves zstd libncurses-dev

```


```bash
tar xf linux-*.tar
cd linux-*/
patch -p1 < ../patch-*.patch
cp -v /boot/config-$(uname -r) .config
make olddefconfig
make menuconfig
```
The second command brings up a terminal interface in which you can configure the preemption model. Navigate with the arrow keys to General Setup > Preemption Model and select Fully Preemptible Kernel (Real-Time).

After that navigate to Cryptographic API > Certificates for signature checking (at the very bottom of the list) > Provide system-wide ring of trusted keys > Additional X.509 keys for default system keyring

Remove the “debian/canonical-certs.pem” from the prompt and press Ok. Save this configuration to .config and exit the TUI.
```
sudo gedit .config
```
remove CONFIG_MODULE_SIG_ALL,CONFIG_MODULE_SIG_KEYCONFIG_SYSTEM_TRUSTED_KEYS,CONFIG_DEBUG_INFO=y
```
make -j$(nproc) deb-pkg
sudo dpkg -i ../linux-headers-*.deb ../linux-image-*.deb
```
```
uname -a
```
```
sudo addgroup realtime
sudo usermod -a -G realtime $(whoami)
```
Afterwards, add the following limits to the realtime group in /etc/security/limits.conf:
```
@realtime soft rtprio 99
@realtime soft priority 99
@realtime soft memlock 102400
@realtime hard rtprio 99
@realtime hard priority 99
@realtime hard memlock 102400
```
Reference:

https://frankaemika.github.io/docs/installation_linux.html
