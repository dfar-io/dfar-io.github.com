---
title: Installing Arch Linux
author: dfar

date: 2021-02-01T17:19:44+00:00
url: /installing-arch-linux/

---
 

## Setting up USB Boot Media

* [Arch Linux](https://wiki.archlinux.org/index.php/USB_flash_installation_medium#Using_basic_command_line_utilities)
* [Windows (using Arch Linux)](https://superuser.com/a/1387874)
* Windows &#8211; use [Rufus][1] (you may need to use GPT && DD image mode)

## Windows Installation

When dual-booting both Windows and Arch Linux, install Windows first using a smaller partition and install any software required.

## Installation

First, boot the Arch Linux USB stick onto the computer of choice. After finished loading, you should see a command terminal.

To increase font size during install, use:

`setfont /usr/share/kbd/consolefonts/latarcyrheb-sun32.psfu.gz`

### Connect To Internet

Wired, use `ping` to confirm access.

#### Wireless

Run the following commands:

````
iwctl
device list
station wlan0 get-networks
station wlan0 connect SSID
exit
````

Verify access with `ping google.com`

### Synchronize Clock

To set the clock, run:

````
timedatectl set-ntp true
timedatectl status
````

### Set up Partitions

If needed, set up partitions for the overall system. First, use **parted -l** to determine both:

  * Which disk you want to use
  * Whether partition table is GPT

If needed, convert to GPT using **fdisk _disk_**.

Use **cfdisk** to create the following partitions (remember when sizing, you can use +512M for ease):

* Linux Swap (8GB)
* Linux root (x86-84) - remaining

### Format & Mount Partitions

Now format the partitions (using **parted -l** to list disks):

````
mkfs.ext4 PRIMARY_DISK
mkswap SWAP_DISK
swapon SWAP_DISK
````

Mount the newly formatted partitions:

````
mount PRIMARY_DISK /mnt
mkdir /mnt/boot
mount EFI_DISK /mnt/boot
````

### Perform Arch Installation

Install base Arch Linux:

````
pacstrap /mnt base linux linux-firmware nano grub efibootmgr networkmanager git firefox sudo
````

Generate an `fstab` file:

````
genfstab -U /mnt >> /mnt/etc/fstab
````

Change root into the new system to set time zone and localization:

````
arch-chroot /mnt
````

````
ln -sf /usr/share/zoneinfo/America/Detroit /etc/localtime
hwclock --systohc
````

Change the default console font size at **/etc/vconsole.conf**:

`FONT=latarcyrheb-sun32`

Generate locales and set locale:

````
# comment out needed locales
nano /etc/locale.gen
locale-gen
````

````
nano /etc/locale.conf
# set content to LANG=en_US.UTF-8
````

Set up network configuration (computer name):

````
# set computer name
nano /etc/hostname
````

````
# configure hosts file, add contents below
nano /etc/hosts

127.0.0.1	localhost
::1		localhost
127.0.1.1	YOUR_HOSTNAME.localdomain	YOUR_HOSTNAME
````

Create a root password with `passwd`.

Configure GRUB boot loader:

`grub-install /dev/sda --efi-directory=/boot`

Add the Windows partition to boot menu options in the file `/etc/grub.d/40_custom`:

```
menuentry "Windows 10" {
    search --fs-uuid --no-floppy --set=root UUID_OF_EFI_PARTITION
    chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
}
```

`grub-mkconfig -o /boot/grub/grub.cfg`

### (Optional) Create Non-Root User

You'll want to perform your daily activities without using the root user.

```
useradd -m dfar
passwd dfar
usermod -aG wheel,audio,video,optical,storage dfar
```

Edit the **/etc/sudoers** file and allow users of `wheel` group to execute any command.

## Conclusion

Finally, reboot the system:

```
exit
shutdown now
```

To verify, remove the USB stick used for installation, and turn on the computer.

You should be able to log in as either the root user or the newly created user specified earlier. If you end up at the terminal, you&#8217;ve successfully installed Arch Linux!

Next step involves [configuring the base system][2].

 [1]: https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_Rufus
 [2]: https://dfar.io/configuring-arch-linux/
