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

------
    

## Partitioning the disks

-----

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


##### Mount the partitions to the Arch USB
Mount the root filesystem to `/mnt`

`$ mount /dev/sda2 /mnt`

After, make a boot inside your root filesystem and an efi folder inside of that:

`$ mkdir /mnt/boot /mnt/boot/efi`

Mount your boot drive to `/mnt/boot/efi`

`$ mount /dev/sda1 /mnt/boot/efi`

-----

## Installation

-----

#### Select your mirror

Check out the mirrors in `/etc/pacman.d/mirrorlist`

`$ vim /etc/pacman.d/mirrorlist`

take the line that has the mirror closest to you and put it at the top of the file, uncommented.
Ex with `cat`:
`$ cat /etc/pacman.d/mirrorlist`

```
Server = https://mirrors.kernel.org/archlinux/$repo/os/$arch
```

More info [here](https://wiki.archlinux.org/index.php/mirrors#Enabling_a_specific_mirror)

------

#### Install the base and base-devel packages

`$ pacstrap /mnt base base-devel`

-----

## Configure your system


#### Generate your [fstab](https://wiki.archlinux.org/index.php/Fstab)

`$ genfstab -U /mnt >> /mnt/etc/fstab `

`-U` is by [UUID](https://wiki.archlinux.org/index.php/UUID), it's more consistent than `-L` which is label

-----

#### [Chroot](https://wiki.archlinux.org/index.php/Change_root) into your system

`$ arch-chroot /mnt `

This puts you into your filesystem (mounted on the USB)

-----

#### Set the [Time Zone](https://wiki.archlinux.org/index.php/Time_zone)

`$ ln -sf /usr/share/zonefo/America/Chicago /etc/localtime`
`$ hwclock --systohc `

This is for Central time, for your time, check the Time Zone link at the header.

-----

#### Set up the [Locale](https://wiki.archlinux.org/index.php/Locale)
`$ echo LANG=en_US.UTF-8 > /etc/locale.conf`
`$ locale-gen `
`$ export LANG=en_US.UTF-8`


-----

#### Network Configuration


-----

##### Create a [hostname](https://wiki.archlinux.org/index.php/Hostname)

`$ {enter your hostanme here) > /etc/hostname`

Your hostname is the word that comes after the @ in your terminal, enter your hostnmae in the `{enter your hostanme here)`

Ex: `vanities`

`user@vanities`

-----


##### Set your /etc/hosts

`$ printf "127.0.0.1	localhost" > /etc/hosts` 
    
    
-----
    
#### Set up [initramfs](https://wiki.archlinux.org/index.php/Mkinitcpio)<br>

`$ mkinitcpio -p linux`

-----

#### Set your root password

`$ passwd`


#### Set up internet

``` systemctl enable dhcpcd  ```


#### Install and Set up grub
`$ pacman -S grub efibootmgr dosfstools os-prober mtools`
`$ grub-install --target=x86_64-efi --efi-directory=/boot/efi --recheck`
`$ grub-mkconfig -o /boot/grub/grub.cfg`


#### Set up Pacman

`$ nano /etc/pacman.conf`

-----

#### download sudo

`$ pacman -S sudo`

`$ visudo`

-----


#### add user
`$ useradd -mg users -G wheel -s /bin/zsh {enter your user here}`
`$ passwd {enter your user here}`


-----

#### install zsh
`$ pacman -S zsh`

-----

## Post-Installation Programs
*
*

