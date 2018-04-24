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
make fs for linux<br>
``` mkfs.ext4 /dev/sda2 ```<br>
``` mkfs.ext4 /dev/sda4 ```<br>
<br>
make swap on<br>
``` mkswap /dev/sda3 ```<br>
``` swapon /dev/sda3 ```<br>
<br>
mount rootfs<br>
``` mount /dev/sda2 /mnt ```<br>
mount boot<br>
``` mkdir /mnt/boot ```<br>
``` mount /dev/sda1 /mnt/boot ```<br>
mount home<br>
``` mkdir /mnt/home ```<br>
``` mount /dev/sda4 /mnt/home ```<br>
<br>
## Configuration and Installation
install base system<br>
``` pacstrap /mnt base base-devel```<br>
generated fstab<br>
``` genfstab -U /mnt >> /mnt/etc/fstab ```<br>
chroot into system<br>
``` arch-chroot /mnt ```<br>
set the timezone<br>
``` ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime ```<br>
``` hwclock --systohc ```<br>
set up locale<br>
``` echo LANG=en_US.UTF-8 > /etc/locale.conf ```<br>
``` locale-gen ```<br>
``` export LANG=en_US.UTF-8 ```<br>
nano /etc/hostname<br>
``` echo vanities > /etc/hostname ```<br>
nano /etc/hosts<br>
    127.0.0.1	localhost<br>
    ::1		localhost<br>
    127.0.1.1	myhostname.localdomain	myhostname<br>
set up initramfs<br>
``` mkinitcpio -p linux ```<br>
set root password<br>
``` passwd ```<br>
set up internet<br>
``` systemctl enable dhcpcd  ```<br>
set up grub<br>
``` pacman -S grub efibootmgr dosfstools os-prober mtools ```<br>
``` mkdir /boot/EFI ```<br>
``` grub-install --target=x86_64-efi --efi-directory=/boot/efi --recheck ```<br>
``` grub-mkconfig -o /boot/grub/grub.cfg ```<br>
set up pacman<br>
``` nano /etc/pacman.conf ```<br>
uncomment multilib<br>
``` pacman -Syu ```<br>
download sudo<br>
``` pacman -S sudo ```<br>
``` visudo ```<br>
add user<br>
``` useradd -mg users -G wheel,storage,power -s /bin/zsh vanities ```<br>
``` passwd your_new_user ```<br>
change if needed<br>
install zsh<br>
``` pacman -S zsh ```<br>
<br>
## Post-Installation Programs
*
*

