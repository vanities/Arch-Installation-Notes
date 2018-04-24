# Arch-Installation-Notes
This is a step by step guide that I use to install Arch Linux

## Initialization
#### check uefi boot mode<br>
``` ls /sys/firmware/efi/efivars ```


#### check internet<br>
``` ping google.com ```
    
    
#### update clock<br>
``` timedatectl set-ntp true ```
    

## Partitioning
#### check the hard disks<br>
``` fdisk -l ```


#### use cfdisk to partition<br>
``` cfdisk /dev/sda ```



| name  | partition |  type               |   size  |
|-------|-----------|---------------------|---------|
| /boot | /dev/sda1 |  EFI System         |  550M   |
| /     | /dev/sda2 |  Linux x86-64 root  |   64G   |
| swap  | /dev/sda3 |  Linux swap         |  8G     |
| /home | /dev/sda4 |  Linux              |   Rest  |



#### make fs for linux<br>
```mkfs.ext4 /dev/sda2```
```mkfs.ext4 /dev/sda4```


#### make swap on<br>
``` mkswap /dev/sda3 ```<br>
``` swapon /dev/sda3 ```


#### mount rootfs<br>
``` mount /dev/sda2 /mnt ```


#### mount boot<br>
``` mkdir /mnt/boot ```<br>
``` mount /dev/sda1 /mnt/boot ```


#### mount home<br>
``` mkdir /mnt/home ```<br>
``` mount /dev/sda4 /mnt/home ```


## Configuration and Installation
install base system<br>
``` pacstrap /mnt base base-devel```


#### generated fstab<br>
``` genfstab -U /mnt >> /mnt/etc/fstab ```


#### chroot into system<br>
``` arch-chroot /mnt ```


#### set the timezone<br>
``` ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime ```<br>
``` hwclock --systohc ```


#### set up locale<br>
``` echo LANG=en_US.UTF-8 > /etc/locale.conf ```<br>
``` locale-gen ```<br>
``` export LANG=en_US.UTF-8 ```


#### nano /etc/hostname<br>
``` echo vanities > /etc/hostname ```


#### nano /etc/hosts<br>
> 127.0.0.1	localhost<br>
> ::1		localhost<br>
> 127.0.1.1	myhostname.localdomain	myhostname
    
    
#### set up initramfs<br>
``` mkinitcpio -p linux ```


#### set root password<br>
``` passwd ```


#### set up internet<br>
``` systemctl enable dhcpcd  ```


#### set up grub<br>
``` pacman -S grub efibootmgr dosfstools os-prober mtools ```<br>
``` mkdir /boot/EFI ```<br>
``` grub-install --target=x86_64-efi --efi-directory=/boot/efi --recheck ```<br>
``` grub-mkconfig -o /boot/grub/grub.cfg ```


#### set up pacman<br>
``` nano /etc/pacman.conf ```


#### uncomment multilib<br>
``` pacman -Syu ``


#### download sudo<br>
``` pacman -S sudo ```<br>
``` visudo ```


#### add user<br>
``` useradd -mg users -G wheel,storage,power -s /bin/zsh vanities ```<br>
``` passwd your_new_user ```


#### install zsh<br>
``` pacman -S zsh ```


## Post-Installation Programs
*
*

