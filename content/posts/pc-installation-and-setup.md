---
title: "PC Installation and Setup"
date: 2023-09-14T18:43:08Z
draft: false
---

## Create Windows Installation Media

Via Windows, use [Media Creator](https://support.microsoft.com/en-us/windows/create-installation-media-for-windows-99a58364-8c02-206f-aa6f-40c3b507420d).

## Install Windows

1. Create EFI partition
   1. Shift + F10
   2. `diskpart`
   3. `list disk`
   4. `select disk <X>`
   5. `clean`
   6. `convert gpt`
   7. `create partition eft size=500`
   8. `exit`
2. Create ~400GB partition.

## Configure Windows

1. Setup multi monitors.
2. Set timezone.
3. Install:
   1. Steam (login, download any games)
   2. Dropbox (login)
   3. Procrastitracker
   4. SHapr3D (login)
   5. VSCode (settings sync)
   6. Discord (login)
   7. Cura (login)

## Create Arch Linux Installation Media

Via Windows:

1. Download Arch Linux ISO
2. Download & Install Rufus
   1. Write ISO in GPT/DD Mode.

## Install Arch Linux

1. `ping dfar.io` (confirm internet)
2. `timedatectl set-ntp true`
3. `timedatectl status`
4. Create partitions
   1. `parted -l`
   2. `cfdisk <DISK>`
      1. 8Gb Linux Swap
      2. Remaining Linux Root (x86-64)
5. Format & mount partitions
   1. `mkfs.ext4 <PRIMARY>`
   2. `mkswap <SWAP>`
   3. `swapon <SWAP>`
   4. `mount <PRIMARY> /mnt`
   5. `mkdir /mnt/boot`
   6. `mount <EFI> /mnt/boot`
6. Install base linux
   1. `pacstrap /mnt base linux linux-firemware nano grun efibootmgr networkmanager git firefox sudo wget cron xorg xterm xorg-xinit xorg-xclock xorg-twm base-devel awesome lightdm lightdm-gtk-greeter nvidia nvidia-settings pkg-config mesa arandr nautilus flameshot evince gnome-calculator numlockx vlc pulseaudio pasystray pavucontrol openvpn network-manager-applet networkmanager-openvpn remmina freerdp sane imagescan gnome-keyring`
7. Configure base linux
   1. `genfstab -U /mnt >> /mnt/etc/fstab`
   2. `arch-chroot /mnt`
   3. `ln -sf /usr/share/zoneinfo/America/Detroit /etc/localtime`
   4. `hwclock --systohc`
   5. `nano /etc/locale.gen` (uncomment locale)
   6. `locale-gen`
   7. `nano /etc/locale.conf` (LANG=en_US.UTF-8)
   8. `nano /etc/hostname`
   9. `nano /etc/hosts` (add 1st code block)
   10. `passwd`
   11. `grub-install <EFI> --efi-directory=/boot`
   12. Add Windows to Boot
       1.  `blkid` (find UUID of EFI)
       2.  `nano /etc/grub.d/40_custom` (add 2nd code block below)
       3.  `grub-mkconfig -o /boot/grub/grub.cfg`

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   dfar-desktop.localdomain dfar-desktop
```

```
menuentry "Windows 11" {
   search --fs-uuid --no-floppy --set=root <EFI_UUID>
   chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
}
```

1.  Setup user
    1.  `useradd -m dfar`
    1.  `passwd dfar`
    2.  `usermod -aG wheel,audio,video,optical,storage dfar` 
    3.  `nano /etc/sudoers` (allow wheel group sudo access)
    4.  Remove thumbdrive, `exit`, `reboot`

## Configure Arch Linux

1. Login with `dfar`
2. `sudo systemctl start NetworkManager`
3. `sudo systemctl enable NetworkManager`
4. `sudo systemctl start lightdm.service`
5. `sudo systemctl enable lightdm.service`
6. Setup Monitors
   1. `arandr` (save result to primary.sh)
   2. `nano ~/.xprofile` (./.screenlayout/primary.sh)
   3. `reboot`
7. Install yay
   1. `git clone https://aur.archlinux.org/yay.git`
   2. `cd yay`
   3. `makrpkg -si`
   4. `cd..`
   5. `rm -rf yay`
8. Install Dropbox
   1. `wget https://linux.dropbox.com/fedora/rpm-public-key.asc`
   2. `gpg --import rpm-public-key.asc`
   3. `rm rpm-public-key.asc`
   4. `yay -S dropbox`
9. Configure Dropbox
   1.  Sign-in
   2.  `mkdir ~/.config/awesome`
   3.  `ln -s ~/Dropbox/config/rc.lua ~/.config/awesome/rc.lua`
   4.  `ln -s ~/Dropbox/config/Xresources-desktop ~/.Xresources`
   5.  `reboot`
10. Install yay packages
    1.  `yay -S visual-studio-code-bin insomnia azuredatastudio-bin`
11. Final touches
    1.  Firefox
    2.  Git (config)
    3.  Set up VPN/Remmina
    4.  VSCode (settings sync)
