---
title : Setup ArchLinux in VirtualBox with LVM
categories: Linux, Arch, Devops
tags: devops, arch, linux
---

# Arch Linux Installation with LVM

## VM Configuration
1. 8GB Ram
2. 20 GB (Intial HDD)
3. Intel CPU 
4. BIOS Mode (For UEFI Please refer official documentation)
(Above settings can be customized for your need.)

General Guidance for HDD location = (Ramsize * 1.5) Swap + .5 GB + 2 GB (OS) + Other apps 

### Steps
1. Boot with Latest Arch Linux ISO Image
2. Execute the following commands
    * ```bash timedatectl set-ntp true ```
    * ```lsblk```       // to check hardisk name (in my case sda, check), If your's different than mine, replace all the below commands with 
    * ```fdisk /dev/sda```
3. Fdisk Setup
    * 512 Boot (n, default, default,default,+512M, a, p )
    * 12 GB Swap (n, default, default,default,+12G,  p )
    * remaining root (n, default, default, default, default, p,t,3,8e)
    * enter "p" to review 
    * enter "w" to write & exit the partition information
    * lsblk to verifiy                  
4. LVM Setup
    * ```lvmdiskscan```
    * ```pvcreate /dev/sda3```
    * ```vgcreate vgroot /dev/sda3```
    * ```lvcreate -L 7G vgroot -n lvroot```

5. Format and Mount Drive
    * ```mkfs.ext4 /dev/sda1```
    * ```mkfs.ext4 /dev/vgroot/lvroot```
    * ```mkswap /dev/sda2```
    * ```swapon /dev/sda2```
    * ```mount /dev/vgroot/lvroot /mnt ```
    * ```mkdir /mnt/boot ```
    * ```mount /dev/sda1 /mnt/boot ```

6. Installation
    * Setup mirror edit "/etc/pacman.d/mirrorlist" and bring the mirror server closest to your region on top. (Optional Step)
    * ```pacstrap /mnt base linux linux-headers linux-firmware base-devel vim lvm2 dhcpcd netctl dialog wpa_supplicant```
    * ```genfstab -U /mnt >> /mnt/etc/fstab```
    * ```arch-chroot /mnt```
    * ```ln -sf /usr/share/zoneinfo/Canada/Eastern /etc/localtime```
    * ```hwclock --systohc```
    * edit /etc/locale.gen and uncomment the locales need to be installed
    * ```locale-gen```
    * ```echo "LANG=en_US.UTF-8" > /etc/locale.conf```
    * ```echo base > /etc/hostname```

7. Install Bootloader (Grub)
    * ```pacman -S grub ```
    * ```grub-install --target=i386-pc /dev/sda```
    * ```grub-mkconfig -o /boot/grub/grub.cfg```

8. Finishing Installation
    * Edit file /etc/mkinitcpio.conf and add systemd & sd-lvm2 hooks 
        * HOOKS=(base <b>systemd</b> ... block <b>lvm2</b> filesystems)
    * ```mkinitcpio -p linux```
    * set password for root using ```passwd```
    * exit
    * reboot (umount the installation cd from VM)

9. Post Installation
    * enable DHCP Service
    * ``` systemctl enable dhcpcd ```
    * ``` systemctl start dhcpcd ```
