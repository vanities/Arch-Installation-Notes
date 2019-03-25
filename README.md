# Relevant one stop shop for Arch Installations
This is a step by step guide that I use to install Arch Linux, this is with [UEFI](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface).

As an overview, here is the Arch Installation guide Table of Contents:
1. Pre-installation
    1. Verify signature
    2. Boot the live environment
    3. Set the keyboard layout
    4. Verify the boot mode
    5. Connect to the internet
    6. Update the system clock
    7. Partition the disks
        1. Example layouts
    8. Format the partitions
    9. Mount the file systems

2. Installation
    1. Select the mirrors
    2. Install the base packages

3. Configure the system
    1. Fstab
    2. Chroot
    3. Time zone
    4. Localization
    5. Network configuration
    6. Initramfs
    7. Root password
    8. Boot loader


4. Reboot
5. Post-installation
-------

## Pre-installation
#### Verify signature of the Arch iso

##### Download the iso
This requires downloading the arch iso first, lol. 
Download it [here](https://www.archlinux.org/download/).

##### Verify the iso
>It is recommended to verify the image signature before use, especially when downloading from an HTTP mirror, where downloads are generally prone to be intercepted to serve malicious images. 

>On a system with GnuPG installed, do this by downloading the PGP signature (under Checksums) to the ISO directory, and verifying it with: 

`$ gpg --keyserver pgp.mit.edu --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig`

-------


#### Boot the live environment

##### Burn the arch iso to a flash drive

This requires flashing the iso you just downloaded to a usb device, to do so, use this software: [Ethcher](https://www.balena.io/etcher/)
Etcher is a free iso burner, that takes three clicks to burn an iso, super easy.

##### Boot the drive from your computer

Load the usb first in your bios section..
[here](https://www.youtube.com/watch?v=xzd73vS9WXo) is a video guide for that.
[here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/boot-to-uefi-mode-or-legacy-bios-mode) is a good text guide for that.

-------


#### Set the keyboard layout
The keyboard is already set for US.
If you're using something other than this, check the instructions out here: [keyboard layouts](https://wiki.archlinux.org/index.php/Installation_guide#Set_the_keyboard_layout)

-------


#### Verify UEFI boot mode
`$ ls /sys/firmware/efi/efivars`
Some stuff should print here, if it doesn't, you booted in legacy (BIOS) mode, or your motherboard doesn't support UEFI.

Help can be found [here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/boot-to-uefi-mode-or-legacy-bios-mode)

-------


#### Connect to the Internet

If you're plugged in to ethernet, you probably already have internet. Verify with this:

`$ ping google.com`

If you don't know what an ethernet cable is, you're probably using wifi connect to it with this command and follow the prompts:

`$ nmtui`

 -------


#### Update the system clock
This makes sure your clock is synced with any server's clocks

`$ timedatectl set-ntp true`
    

## Partitioning the disks

#### Check your hard disks
This lists out a table of your connected harddrives and usbs, most harddrives start with sd*
for example, sda, sdb, sdc, etc.

nvme harddrives start like this:
nvme01, nvme02, etc.

`$ fdisk -l`


#### Use cfdisk to partition
Like cutting a pie into different sections, we too but the disk into two sections,
1. the `/boot` section
    1. you boot from this section
2. the `/` or root section
    1. this is where your system and user files live

`$ cfdisk /dev/sda`


| name  | partition |  type               |   size        |
|-------|-----------|---------------------|---------------|
| /boot | /dev/sda1 |  EFI System         |  550M         |
| /     | /dev/sda2 |  Linux x86-64 root  |  Rest of GB   |



#### Make the root directory an ext4 partition

`$ mkfs.ext4 /dev/sda2`


#### Mount the partitions to the Arch USB
Mount the root filesystem to    `/mnt`
`$ mount /dev/sda2 /mnt`

After, make a boot folder inside your root filesystem:
`$ mkdir /mnt/boot`



## Configuration and Installation
#### install base system<br>
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

