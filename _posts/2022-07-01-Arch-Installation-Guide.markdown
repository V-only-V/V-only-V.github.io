# Arch Linux Installation Guide by g0ng0

## Arch Linux is a Linux distribution. A simple, light and flexible distribution. It follows the KISS (Keep It Simple, Stupid) principle.

### Website Official of Arch Linux for more information

[Arch Linux](https://archlinux.org/)

---

### Arch Linux Requirements

### Minimum

- x86_64 / 64 bit
- 512 MB Ram
- 800 MB Disk Space
- Internet Connection

### Recommended

- x86_64 / 64 bit
- 2 GB Ram
- 20 GB Disk Space
- Internet Connection

### Previous knowledge

- Operating system
- Linux distributions
- Linux

---

## Installation

### Downloading ISO

> Arch Linux Downloads
> 

[Arch Linux](https://archlinux.org/download/)

---

### Prepare an installation medium

> The installation image can be supplied to the target machine via a USB flash drive
> 

> Once you have the ISO downloaded you have to prepare it on an optical disk or USB, I recommend better by USB, you have to burn the ISO on the USB with a program. I recommend two.
> 

[Rufus](https://rufus.ie/en/)

[balenaEtcher - Flash OS images to SD cards & USB drives](https://www.balena.io/etcher/)

<aside>
💡 Note: Arch Linux installation images do not support Secure Boot. You will need to disable Secure Boot to boot the installation medium, if desired, Secure Boot can be set up after completing the installation

</aside>
<br>
<aside>
⚠️ Attention at this point you are about to start installing Arch, you must have a backup on your computer or back up all your files in case something goes wrong, this if you are going to install it on hardware. If you are going to install it in a virtual machine there is nothing to worry about.

</aside>
<br>

> You have to insert the USB into your computer, reboot it and enter the bios of your computer. Once there you have to go to the boot section and put the USB first in the boot list, then save and exit. This will boot the Arch Linux ISO.
> 

---

### Set the console keyboard layout

<aside>
💡 The default console keymap is US

</aside>
<br>
<aside>
💡 List all keyboard layouts

</aside>
<br>

``````
ls /usr/share/kbd/keymaps/**/*.map.gz
``````

<br>
<aside>
💡 load your keyboard with

</aside>

```html
loadkeys "yourdistribution"
```
<br>
<aside>
💡 You have to put the 'loadkeys' followed by the name of the file that you listed without the .map.gz

</aside>
<br>
<aside>
💡 to load some keyboards

</aside>
<br>

```markdown
> azerty
loadkeys azerty
```

```markdown
> latin
loadkeys de-latin1
```

```markdown
> dvorak
loadkeys dvorak
```

<aside>
💡 Console fonts are located in /usr/share/kbd/consolefonts/ and can likewise be set with setfont

</aside>

---

## Verify the boot mode

<aside>
💡 To verify the boot mode, list the efivars directory

</aside>
<br>

```markdown
ls /sys/firmware/efi/efivars
```

<br>
<aside>
💡 if the command shows the directory without error, then the system is booted in UEFI mode. if the directory does not exist, the system may be booted in BIOS mode. If the system did not in the mode you desired, refer to your motherboard’s manual

</aside>

---

## Connect to the internet

> To set up a network connection in the live environment, go through the following steps:
> 

<aside>
💡 Ensure your network interface is listed and enabled, for example with ip-link

</aside>
<br>

```markdown
ip link
```

- Connect to the network
    - Ethernet—plug in the cable
    - WI-FI—authenticate to the wireless network using iwctl
    - Mobile broadband modem—connect to the mobile network with the mmcli utility

<aside>
💡 The connection may be verified with ping

</aside>
<br>

```markdown
ping archlinux.orch
```

<br>
<aside>
💡 You can also ping another web site or a dns

</aside>
<br>

```markdown
ping 8.8.8.8   
ping www.google.com
```

<br>
<aside>
💡 ctrl + c kill ping

</aside>

 * If you are going to install the operating system on a laptop I recommend that you connect the Ethernet cable to it but if not, visit [https://wiki.archlinux.org/title/Iwd#iwctl](https://wiki.archlinux.org/title/Iwd#iwctl) there is a lot of information on how you are going to do with the WIFI


---

## Update the system clock

> Use timedatectl to ensure the system clock is accurate:
> 

```markdown
timedatectl set-ntp true
```

<br>
<aside>
💡 To check the service status, use:

</aside>
<br>

```
timedatectl status
```

---

## Partition the disk

> cfdisk   |   It is a graphical terminal program to make partitioning much more simple
<br>
> fdisk   |   This is the manual way so to speak to make the partitions
> 

> When recognize by the live system, disks are assigned to a block device such as /dev/sda , /dev/nvme0n1 or /dev/mmcblk0. To identify these devices, use lsblk or fdisk
> 

```markdown
fdisk -l
```

> One partition for the root directory /
> 

> For booting in UEFI mode: an EFI system partition
> 

> if you want to create any stacked block devices for LVM, system encryption or RAID, do it now
> 

> Use fdisk or parted to modify partition tables. For example:
> 

```markdown
fdisk /dev/the_disk_to_be_partitioned
```

<br>
<aside>
💡 Note: if the disk does not show up, make sure the disk controller is not in RAID mode

</aside>

<br>
<aside>
💡 if the disk from which you want to boot already has an EFI system partition, do not create another one, but use the existing partition instead

</aside>

<br>
<aside>
💡 Swap space can be set on a swap file for files systems supperting it

</aside>

### UEFI with GPT

| Mount point | Partition | Partition type | Suppested size |
| --- | --- | --- | --- |
| /mnt/boot | /dev/efi_system_partition | EFI system partition | At least 300 MIB |
| [SWAP] | /dev/swap_partition | Linux swap | More than 512 MIB |
| /mnt | /dev/root_partition | Linux | Remainder of the device |

### BIOS with MBR

| Mount point | Partition | Partition type | Suggested size |
| --- | --- | --- | --- |
| [SWAP] | /dev/swap_partition | Linux swap | More than 512 MIB |
| /mnt | /dev/root_partition | Linux | Remainder of the device |

<br>
<aside>
💡 These tables were extracted from the official Arch Linux web site.

</aside>

---

## Format the partitions

> Once the partitions have been created, each newly created partition must be formatted with an appropriate file system. See File systems#Create a file system for details
> 

> For example, to create an EXT4 file system on /dev/root_partition, run:
> 
<br>

```markdown
mkfs.ext4 /dev/root_partition
```

<br>

> if you created a partition for swap, initialize it with mkswap
> 
<br>

```markdown
mkswap /dev/swap_partition
```

<br>
<aside>
💡 Note: For stacked block devices replace /dev/*_partition with the appropriate block dvice path

</aside>

<br>

> If you created an EFI system partition, format it to FAT32 using mkfs.fat
> 

<br>
<aside>
❗ Warning: Only format the EFI system partition if you created it during the partitioning step. If there already was an EFI system partition on disk beforehand, reformatting it can destroy the boot leaders of other installed operating systems

</aside>
<br>

```markdown
mkfs.fat -F 32 /dev/efi_system_partition
```

---

## Mount the file systems

> Mount the root volume to /mnt. For example, if the root volume is /dev/root_partition:
> 
<br>

```markdown
mount /dev/root_partition /mnt
```

<br>

> Create any remainig mount points ( such as /mnt/efi ) using mkdir and mount their correspoding volumes
> 

> For UEFI systems, mount the EFI system partition:
> 
<br>

```markdown
mount /dev/efi_system_partition /mnt/boot
```

<br>

> If you created a swap volume, enable it with swapon
> 
<br>

```markdown
swapon /dev/swap_partition
```

---

## Installation

### Select the mirrors

> Packages to be installed must be downloaded from mirror servers, which are defined in /etc/pacman.d/mirrorlist . On the live system, after connecting to the internet, reflector updates the mirror list by choosing 20 most recently synchonized HTTPS mirrors and sorting them by download rate
> 
<br>

### Install essential packages

> Use the pacstrap script to install the base package, Linux kernel and firmware for common hardware
> 
<br>

```markdown
pacstrap /mnt base linux linux-firmware
```

## Configure the system

### Fstab

> Generate an fstab file (use -U or -L to define by UUID or abels, respectively):
> 
<br>

```markdown
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot

> Change root into the new system:
> 
<br>

```markdown
arch-chroot /mnt
```

### Time zone

> Set the time zone:
> 
<br>

```markdown
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```

<br>

> Run hwclock to generate /etc/adjtime
> 
<br>

```markdown
hwclock --systohc
```

## Localization

> Edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8 and other needed locale. Generate the locales by running:
> 
<br>

```markdown
locale-gen
```

<br>

> Create the locale.conf file, and set the LANG varuable accordingly:
> 
<br>

```markdown
/etc/locale.conf
***
LANG=en_US.UTF-8
```

<br>

> If you set the console keyboard layout, make the changes persistent in vconsole.conf
> 
<br>

```markdown
/etc/vconsole.conf
***
KEYMAP=de-latin1
```

## Network Configuration

> Create the hostname file
> 
<br>

```markdown
/etc/hostname
***
myhostname
```

## Root password

> Set the root password:
> 
<br>

```markdown
passwd
```

## Reboot

> Exit the chroot environment by typing:
> 
<br>

```markdown
exit
```

<br>

> Optionally manually unmount all the partitions with:
> 

```markdown
umount -R /mnt
```

<br>

> Restart the machine
>
