---
title: Installing Arch Linux
author: dfar

date: 2020-02-08T17:19:44+00:00
url: /installing-arch-linux/
categories:
  - Technology
  - Uncategorized

---
 

## Setting up USB Boot Media

* [Arch Linux](https://wiki.archlinux.org/index.php/USB_flash_installation_medium#Using_basic_command_line_utilities)
* [Windows (using Arch Linux)](https://superuser.com/a/1387874)
* Windows &#8211; use [Rufus][1] (you may need to use GPT && DD image mode)

## Installation

First, boot the Arch Linux USB stick onto the computer of choice. After finished loading, you should see a command terminal.

To increase font size during install, use:

<pre class="wp-block-code"><code>setfont /usr/share/kbd/consolefonts/latarcyrheb-sun32.psfu.gz</code></pre>

### Connect To Internet

Wired, use `ping` to confirm access.

Wireless &#8211; run: `wifi-menu -o`, select a network, then verify access using `ping`.

### Synchronize Clock

To set the clock, run:

<pre class="wp-block-code"><code>timedatectl set-ntp true
timedatectl status</code></pre>

### Set up Partitions

If needed, set up partitions for the overall system. First, use **parted -l** to determine both:

  * Which disk you want to use
  * Whether partition table is GPT

If needed, convert to GPT using **fdisk _disk_**.

Use **cfdisk** to create the following partitions (remember when sizing, you can use +512M for ease):<figure class="wp-block-table is-style-stripes">

<table class="">
  <tr>
    <td>
      Mount Point
    </td>
    
    <td>
      Partition
    </td>
    
    <td>
      Partition Type
    </td>
    
    <td>
      Size
    </td>
  </tr>
  
  <tr>
    <td>
      /mnt/boot
    </td>
    
    <td>
      /dev/sda1
    </td>
    
    <td>
      EFI System
    </td>
    
    <td>
      512MB
    </td>
  </tr>
  
  <tr>
    <td>
      /mnt
    </td>
    
    <td>
      /dev/sda2
    </td>
    
    <td>
      Linux root (x86-64)
    </td>
    
    <td>
      remaining
    </td>
  </tr>
  
  <tr>
    <td>
      SWAP
    </td>
    
    <td>
      /dev/sda3
    </td>
    
    <td>
      Linux swap
    </td>
    
    <td>
      8GB
    </td>
  </tr>
</table></figure> 

### Format & Mount Partitions

Now format the partitions (using **parted -l** to list disks):

<pre class="wp-block-code"><code>mkfs.fat &lt;EFI_DISK>
mkfs.ext4 &lt;PRIMARY_DISK>
mkswap &lt;SWAP_DISK>
swapon &lt;SWAP_DISK></code></pre>

Mount the newly formatted partitions:

<pre class="wp-block-code"><code>mount &lt;PRIMARY_DISK> /mnt
mkdir /mnt/boot
mount &lt;EFI_DISK> /mnt/boot</code></pre>

### Perform Arch Installation

Edit the `/etc/pacman.d/mirrorlist` file and set it to have just 12 United States entries.

Install base Arch Linux:

<pre class="wp-block-code"><code>pacstrap /mnt base linux linux-firmware nano grub efibootmgr networkmanager git firefox sudo</code></pre>

Generate an `fstab` file:

<pre class="wp-block-code"><code>genfstab -U /mnt >> /mnt/etc/fstab</code></pre>

Change root into the new system to set time zone and localization:

<pre class="wp-block-code"><code>arch-chroot /mnt

ln -sf /usr/share/zoneinfo/America/Detroit /etc/localtime
hwclock --systohc</code></pre>

Change the default console font size at **/etc/vconsole.conf**:

<pre class="wp-block-code"><code>FONT=latarcyrheb-sun32</code></pre>

Generate locales and set locale:

<pre class="wp-block-code"><code># comment out needed locales
nano /etc/locale.gen
locale-gen

nano /etc/locale.conf

# set content to LANG=en_US.UTF-8</code></pre>

Set up network configuration (computer name):

<pre class="wp-block-code"><code># set computer name
nano /etc/hostname 

# configure hosts file, add contents below
nano /etc/hosts

127.0.0.1	localhost
::1		localhost
127.0.1.1	YOUR_HOSTNAME.localdomain	YOUR_HOSTNAME</code></pre>

Create a root password with `passwd`.

Configure GRUB boot loader:

<pre class="wp-block-code"><code>grub-install /dev/sda --efi-directory=/boot

grub-mkconfig -o /boot/grub/grub.cfg</code></pre>

### (Optional) Create Non-Root User

You&#8217;ll want to perform your daily activities without using the root user.

<pre class="wp-block-code"><code>useradd -m dfar
passwd dfar
usermod -aG wheel,audio,video,optical,storage dfar</code></pre>

Edit the **/etc/sudoers** file and allow users of `wheel` group to execute any command.

## Conclusion

Finally, reboot the system:

<pre class="wp-block-code"><code>exit

shutdown now</code></pre>

To verify, remove the USB stick used for installation, and turn on the computer.

You should be able to log in as either the root user or the newly created user specified earlier. If you end up at the terminal, you&#8217;ve successfully installed Arch Linux!

Next step involves [configuring the base system][2].

 [1]: https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_Rufus
 [2]: https://dfar.io/configuring-installed-arch-linux/
