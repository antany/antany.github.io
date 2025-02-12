# How to install Arch Linux on removable media with UEFI & Legacy boot, that can virtually run on any PC
References:
https://wiki.archlinux.org/index.php/Installation_guide
https://wiki.archlinux.org/index.php/Install_Arch_Linux_on_a_removable_medium
https://wiki.archlinux.org/index.php/Multiboot_USB_drive'


### Step 1: Required softwares
1. Virtual Box (https://www.virtualbox.org/wiki/Downloads) (Install the latest version)
2. Virtual Box extension pack (https://www.virtualbox.org/wiki/Downloads) version matching virtual box
3. Arch Linux ISO A [Download](https://www.archlinux.org/download/) Download ISO from any peferrable way
4. Install Virtual Box and Extension pack

### Step 2: Create new VM 
1. Open Virtual Box, Create new VM by selecting Machine -> New and enter the following values and select create
  * Name    : (Anyname you like) , I am using Arch
  * Type    : Linux
  * Version : Arch Linux (64-Bit) (if you dont see 64 Bit, make sure hyperviser is disable in windows and virtualization opion is enabled in bios)
  * Click create - leave others to default
  * In Create Virual Hard Disk, Click create - leave others to default. 
 
### Step 3: Booting with Arch Linux and add our USB
Note : When selecting USB drive, pick the one with high read write speed preferrably USB 3.0 drive. If you have slow drive its still fine, but will be slower than high speed drive.

1. Go to VM Settings->USB->(USB 3.0 (xHCI) Controller) (pick USB 2.0 if you are using USB 2.0)->add icon->Select your USB drive-> Ok 
2. Start the VM
3. First time it will ask you pick up the bootable media, choose the iso you have downloaded from arch linux
4. After successful boot, you will see ``` root@archiso ~# ```

## Installing Arch

### Step 4: Partitioning
1. ```lsblk ``` you will see list of drives and find the drive name using the size of usb drive
2. In my case its ``` sdb ```, if yours is difference you can replace sdb with whatever you see in your pc, in the upcoming instructions. Note you will also see the drive you have picked up for your virtual box. Dont use that
3. Creating parition & file systems
  * For having both legacy and UEFI support, mininum i need 3 partitions
    1. UEFI Boot
    2. Legacy Boot
    3. Others (OS + Softwares)
```
   gdisk /dev/sdb
   p     //show any available partition
   d     //delete if any paritions available
   n -> enter->enter-> +1MB + EF02
   n -> enter->enter-> +50MB + EF00
   n -> enter->enter->enter->enter
   p to cofirm the parition looks good
   
   Command (? for help): r
   Recovery/transformation command (? for help): h

   WARNING! Hybrid MBRs are flaky and dangerous! If you decide not to use one,
   just hit the Enter key at the below prompt and your MBR partition table will
   be untouched.

   Type from one to three GPT partition numbers, separated by spaces, to be added to the hybrid MBR, in sequence: 1 2 3
   Place EFI GPT (0xEE) partition first in MBR (good for GRUB)? (Y/N): N

   Creating entry for GPT partition #1 (MBR partition #1)
   Enter an MBR hex code (default EF): 
   Set the bootable flag? (Y/N): N

   Creating entry for GPT partition #2 (MBR partition #2)
   Enter an MBR hex code (default EF): 
   Set the bootable flag? (Y/N): N

   Creating entry for GPT partition #3 (MBR partition #3)
   Enter an MBR hex code (default 83): 
   Set the bootable flag? (Y/N): Y

   Recovery/transformation command (? for help): x
   Expert command (? for help): h
   Expert command (? for help): w

   Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
   PARTITIONS!!

   Do you want to proceed? (Y/N): Y
   
```

### Format parition  & Mounting


Leave partition 1 as it is

```
mkfs.fat -F32 /dev/sdb2
mkfs.ext4 -O "^has_journal" /dev/sdb3

mount /dev/sdb3 /mnt
mkdir -p /mnt/boot/EFI
mount /dev/sdb2 /mnt/boot/EFI

```

### Installing OS

 * ``` timedatectl set-ntp true ``` //sync time automatically
 * ``` pacstrap /mnt base linux linux-firmware base-devel vi vim dhcpcd dialog ppp wpa_supplicant netctl```
 * ```genfstab -U /mnt >> /mnt/etc/fstab```
 * ```arch-chroot /mnt```
 * ```ln -sf /usr/share/zoneinfo/Canada/Eastern /etc/localtime```
 * ```hwclock --systohc```
 * edit /etc/locale.gen and uncomment the locales need to be installed
 * ```locale-gen```
 * ```echo "LANG=en_US.UTF-8" > /etc/locale.conf```
 * ```echo base > /etc/hostname```
 * ```passwd ``` //setup password

### Installation Grub & Configuration

 * ``` pacman -S grub efibootmgr ```
 * ``` grub-install --target=x86_64-efi --recheck --removable --efi-directory=/boot/EFI --boot-directory=/boot ```
 * ``` grub-install --target=i386-pc --recheck --boot-directory=/boot /dev/sdb ```
 * ``` grub-mkconfig -o /boot/grub/grub.cfg```
 * ``` mkinitcpio -P linux ```
 * ``` pacman -S  xf86-video-vesa xf86-video-ati xf86-video-intel xf86-video-amdgpu xf86-video-nouveau ```

reboot and try booting with usb.. enjoy..




