
## ubuntu 20.04 install RT kernel & support Nvidia driver 

#### 1. choosing the version closest to the one you currently use. 
   
Kernel:https://mirrors.edge.kernel.org/pub/linux/kernel/

RT patch: https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/

For Ubuntu 20.04 tested with the kernel version 5.15.2:

```bash
curl -SLO https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.15.2.tar.xz
curl -SLO https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.15.2.tar.sign
curl -SLO https://www.kernel.org/pub/linux/kernel/projects/rt/5.15/patch-5.15.2-rt20.patch.xz
curl -SLO https://www.kernel.org/pub/linux/kernel/projects/rt/5.15/patch-5.15.2-rt20.patch.sign
```

#### 2. Install the necessary dependencies:
```bash
sudo apt-get install build-essential bc curl ca-certificates gnupg2 libssl-dev lsb-release libelf-dev bison flex dwarves zstd libncurses-dev
```

#### 3. Add the patch and set configuraion
```bash
tar xf linux-*.tar
cd linux-*/
patch -p1 < ../patch-*.patch
cp -v /boot/config-$(uname -r) .config
make olddefconfig
make menuconfig
```
The second command brings up a terminal interface in which you can configure the preemption model. Navigate with the arrow keys to General Setup > Preemption Model and select Fully Preemptible Kernel (Real-Time).

```bash
scripts/config --disable SYSTEM_REVOCATION_KEYS
scripts/config --disable SYSTEM_TRUSTED_KEYS
make -j$(nproc) deb-pkg
sudo dpkg -i ../linux-headers-*.deb ../linux-image-*.deb
```
reboot
```
uname -a
```
now you can see the rt-kernel(if not change the boot priority through edit "/etc/default/grub" file)

#### 4. add reatime group
```bash
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

#### 5. Install NVIDIA driver

##### a. Download NVIDIA driver as a .run file

##### b. Stop X-Server and into terminal mode(ctrl + Shift+ F1 or F2)
```bash
sudo service lightdm stop
```
##### c. Blacklist Nouveau driver
```bash
sudo vi /etc/modprobe.d/blacklist-nouveau.conf
```
Insert into file:
```
blacklist nouveau
options nouveau modeset=0
```
##### d. Update kernel initramfs
```bash
sudo update-initramfs -u
sudo reboot  # I'm not sure if needed
```
##### e. Install driver!
```bash
sudo IGNORE_PREEMPT_RT_PRESENCE=1 bash <*>.run  # Insert downloaded .run file
```
##### f. Reboot
```bash
sudo reboot
```
#### Reference:

https://frankaemika.github.io/docs/installation_linux.html




