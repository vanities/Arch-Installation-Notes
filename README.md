# Arch-Installation-Notes
This is a step by step guide that I use to install Arch Linux

## Initialization
check uefi boot mode<br>
``` ls /sys/firmware/efi/efivars ```<p>
check internet<br>
``` ping google.com ```<p>
update clock<br>
``` timedatectl set-ntp true ```<p>

## Partitioning
check the hard disks<br>
``` fdisk -l ```<p>
use cfdisk to partition<br>
``` cfdisk /dev/sda ```<p>

| name  | partition |  type               |   size  |
|-------|-----------|---------------------|---------|
| /boot | /dev/sda1 |  EFI System         |  550M   |
| /     | /dev/sda2 |  Linux x86-64 root  |   64G   |
| swap  | /dev/sda3 |  Linux swap         |  8G     |
| /home | /dev/sda4 |  Linux              |   Rest  |

<p>
make fs for linux<br>
```
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda4
```
<p>
make swap on<br>
``` mkswap /dev/sda3 ```<br>
``` swapon /dev/sda3 ```<p>
<br>
mount rootfs<br>
``` mount /dev/sda2 /mnt ```<p>
mount boot<br>
``` mkdir /mnt/boot ```<br>
``` mount /dev/sda1 /mnt/boot ```<p>
mount home<br>
``` mkdir /mnt/home ```<br>
``` mount /dev/sda4 /mnt/home ```<p>
<br>
## Configuration and Installation
install base system<br>
``` pacstrap /mnt base base-devel```<p>
generated fstab<br>
``` genfstab -U /mnt >> /mnt/etc/fstab ```<p>
chroot into system<br>
``` arch-chroot /mnt ```<p>
set the timezone<br>
``` ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime ```<br>
``` hwclock --systohc ```<p>
set up locale<br>
``` echo LANG=en_US.UTF-8 > /etc/locale.conf ```<br>
``` locale-gen ```<br>
``` export LANG=en_US.UTF-8 ```<p>
nano /etc/hostname<br>
``` echo vanities > /etc/hostname ```<p>
nano /etc/hosts<br>
    127.0.0.1	localhost<br>
    ::1		localhost<br>
    127.0.1.1	myhostname.localdomain	myhostname<p>
set up initramfs<br>
``` mkinitcpio -p linux ```<p>
set root password<br>
``` passwd ```<p>
set up internet<br>
``` systemctl enable dhcpcd  ```<p>
set up grub<br>
``` pacman -S grub efibootmgr dosfstools os-prober mtools ```<br>
``` mkdir /boot/EFI ```<br>
``` grub-install --target=x86_64-efi --efi-directory=/boot/efi --recheck ```<br>
``` grub-mkconfig -o /boot/grub/grub.cfg ```<p>
set up pacman<br>
``` nano /etc/pacman.conf ```<p>
uncomment multilib<br>
``` pacman -Syu ``<p>
download sudo<br>
``` pacman -S sudo ``<p>
``` visudo ```<p>
add user<br>
``` useradd -mg users -G wheel,storage,power -s /bin/zsh vanities ```<br>
``` passwd your_new_user ```<p>
install zsh<br>
``` pacman -S zsh ```<p>
<br>
## Post-Installation Programs
*
*

