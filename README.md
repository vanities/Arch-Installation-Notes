# Arch-Installation-Notes
This is a step by step guide that I use to install Arch Linux

## Initialization
check uefi boot mode<br>
``` ls /sys/firmware/efi/efivars ```<br>
check internet<br>
``` ping google.com ```<br>
update clock<br>
``` timedatectl set-ntp true ```<br>

## Partitioning
check the hard disks<br>
``` fdisk -l ```<br>
use cfdisk to partition<br>
``` cfdisk /dev/sda ```<br>
/boot /dev/sda1  EFI System            550M<br>
/     /dev/sda2  Linux x86-64 root     64G<br>
swap  /dev/sda3  Linux swap            8G<br>
/home /dev/sda4  Linux                 Rest<br>
<br>
make fs for linux
``` mkfs.ext4 /dev/sda2 ```
``` mkfs.ext4 /dev/sda4 ```

make swap on
``` mkswap /dev/sda3 ```
``` swapon /dev/sda3 ```

mount rootfs
``` mount /dev/sda2 /mnt ```
mount boot
``` mkdir /mnt/boot ```
``` mount /dev/sda1 /mnt/boot ```
mount home
``` mkdir /mnt/home ```
``` mount /dev/sda4 /mnt/home ```

## Configuration and Installation
install base system
``` pacstrap /mnt base base-devel```
generated fstab
``` genfstab -U /mnt >> /mnt/etc/fstab ```
chroot into system
``` arch-chroot /mnt ```
set the timezone
``` ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime ```
``` hwclock --systohc ```
set up locale
``` echo LANG=en_US.UTF-8 > /etc/locale.conf ```
``` locale-gen ```
``` export LANG=en_US.UTF-8 ```
nano /etc/hostname
``` echo vanities > /etc/hostname ```
nano /etc/hosts
    127.0.0.1	localhost
    ::1		localhost
    127.0.1.1	myhostname.localdomain	myhostname
set up initramfs
``` mkinitcpio -p linux ```
set root password
``` passwd ```
set up internet
``` systemctl enable dhcpcd  ```
set up grub
``` pacman -S grub efibootmgr dosfstools os-prober mtools ```
``` mkdir /boot/EFI ```
``` grub-install --target=x86_64-efi --efi-directory=/boot/efi --recheck ```
``` grub-mkconfig -o /boot/grub/grub.cfg ```
set up pacman
``` nano /etc/pacman.conf ```
uncomment multilib
``` pacman -Syu ```
download sudo
``` pacman -S sudo ```
``` visudo ```
add user
``` useradd -mg users -G wheel,storage,power -s /bin/zsh vanities ```
``` passwd your_new_user ```
change if needed
install zsh
``` pacman -S zsh ```

## Post-Installation Programs
*
*

