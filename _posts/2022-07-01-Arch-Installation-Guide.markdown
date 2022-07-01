# Post I

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

<aside>
⚠️ Attention at this point you are about to start installing Arch, you must have a backup on your computer or back up all your files in case something goes wrong, this if you are going to install it on hardware. If you are going to install it in a virtual machine there is nothing to worry about.

</aside>

> You have to insert the USB into your computer, reboot it and enter the bios of your computer. Once there you have to go to the boot section and put the USB first in the boot list, then save and exit. This will boot the Arch Linux ISO.
> 

---

### Set the console keyboard layout

<aside>
💡 The default console keymap is US

</aside>

<aside>
💡 List all keyboard layouts

</aside>

```markdown
ls /usr/share/kbd/keymaps/**/*.map.gz
```

<aside>
💡 load your keyboard with

</aside>

```html
loadkeys "yourdistribution"
```

<aside>
💡 You have to put the 'loadkeys' followed by the name of the file that you listed without the .map.gz

</aside>

<aside>
💡 to load some keyboards

</aside>

```markdown
# azerty
loadkeys azerty
```

```markdown
# latin
loadkeys de-latin1
```

```markdown
# dvorak
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

```markdown
ls /sys/firmware/efi/efivars
```

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

```markdown
ping archlinux.orch
```

<aside>
💡 You can also ping another web site or a dns

</aside>

```markdown
ping 8.8.8.8   
ping www.google.com
```

<aside>
💡 ctrl + c kill ping

</aside>

<aside>
▫️ Si vas a instalar el sistema operativo en una laptop te recomiendo que le conectes el cable de Ethernet pero si no, visita "[https://wiki.archlinux.org/title/Iwd#iwctl](https://wiki.archlinux.org/title/Iwd#iwctl)" hay mucha informacion de como le vas a hacer con el WIFI

</aside>

---

## Update the system clock

> Use timedatectl to ensure the system clock is accurate:
> 

```markdown
timedatectl set-ntp true
```

<aside>
💡 To check the service status, use timedatectl status

</aside>

---

## Partition the disk

> cfdisk   |   Es un programa de terminal grafico para hacer las particione mucho mas sensilla
fdisk   |   Esta es la manera manual por asi decirlo de hacer las particiones
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

<aside>
💡 Note: if the disk does not show up, make sure the disk controller is not in RAID mode

</aside>

<aside>
💡 if the disk from which you want to boot already has an EFI system partition, do not create another one, but use the existing partition instead

</aside>

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

<aside>
💡 These tables were extracted from the official Arch Linux web site.

</aside>

---

## Format the partitions

> Once the partitions have been created, each newly created partition must be formatted with an appropriate file system. See File systems#Create a file system for details
> 

> For example, to create an EXT4 file system on /dev/root_partition, run:
> 

```markdown
mkfs.ext4 /dev/root_partition
```

> if you created a partition for swap, initialize it with mkswap
> 

```markdown
mkswap /dev/swap_partition
```

<aside>
💡 Note: For stacked block devices replace /dev/*_partition with the appropriate block dvice path

</aside>

> If you created an EFI system partition, format it to FAT32 using mkfs.fat
> 

<aside>
❗ Warning: Only format the EFI system partition if you created it during the partitioning step. If there already was an EFI system partition on disk beforehand, reformatting it can destroy the boot leaders of other installed operating systems

</aside>

```markdown
mkfs.fat -F 32 /dev/efi_system_partition
```

---

## Mount the file systems

> Mount the root volume to /mnt. For example, if the root volume is /dev/root_partition:
> 

```markdown
mount /dev/root_partition /mnt
```

> Create any remainig mount points ( such as /mnt/efi ) using mkdir and mount their correspoding volumes
> 

> For UEFI systems, mount the EFI system partition:
> 

```markdown
mount /dev/efi_system_partition /mnt/boot
```

> If you created a swap volume, enable it with swapon
> 

```markdown
swapon /dev/swap_partition
```

---

## Installation

### Select the mirrors

> Packages to be installed must be downloaded from mirror servers, which are defined in /etc/pacman.d/mirrorlist . On the live system, after connecting to the internet, reflector updates the mirror list by choosing 20 most recently synchonized HTTPS mirrors and sorting them by download rate
> 

### Install essential packages

> Use the pacstrap script to install the base package, Linux kernel and firmware for common hardware
> 

```markdown
pacstrap /mnt base linux linux-firmware
```

## Configure the system

### Fstab

> Generate an fstab file (use -U or -L to define by UUID or abels, respectively):
> 

```markdown
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot

> Change root into the new system:
> 

```markdown
arch-chroot /mnt
```

### Time zone

> Set the time zone:
> 

```markdown
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```

> Run hwclock to generate /etc/adjtime
> 

```markdown
hwclock --systohc
```

## Localization

> Edit /etc/locale.gen and uncomment en_US.UTF-8 UTF-8 and other needed locale. Generate the locales by running:
> 

```markdown
locale-gen
```

> Create the locale.conf file, and set the LANG varuable accordingly:
> 

```markdown
/etc/locale.conf
----------------
LANG=en_US.UTF-8
```

> If you set the console keyboard layout, make the changes persistent in vconsole.conf
> 

```markdown
/etc/vconsole.conf
------------------
KEYMAP=de-latin1
```

## Network Configuration

> Create the hostname file
> 

```markdown
/etc/hostname
-------------
myhostname
```

## Root password

> Set the root password:
> 

```markdown
passwd
```

## Reboot

> Exit the chroot environment by typing:
> 

```markdown
exit
```

> Optionally manually unmount all the partitions with:
> 

```markdown
umount -R /mnt
```

> Restart the machine
>
