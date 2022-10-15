# 1. Download NVIDIA driver as a .run file

# 2. Stop X-Server
sudo service lightdm stop

# 3. Blacklist Nouveau driver
sudo nano /etc/modprobe.d/blacklist-nouveau.conf

# Insert into file:
#  blacklist nouveau
#  options nouveau modeset=0

# 4. Update kernel initramfs
sudo update-initramfs -u
sudo reboot  # I'm not sure if needed

# 5. Install driver!
sudo IGNORE_PREEMPT_RT_PRESENCE=1 bash <*>.run  # Insert downloaded .run file

# 6. Reboot
sudo reboot
