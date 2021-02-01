---
title: Configuring Arch Linux
author: dfar

date: 2021-02-01T02:25:02+00:00
url: /configuring-arch-linux/
categories:
  - Technology
  - Uncategorized

---
If you&#8217;re [installed Arch Linux][1], the next step is configuring the system to work as desired. In this guide, I walk through my general configuration of a newly installed Arch Linux instance.

## Configure Internet 

Set up **networkmanager** and if wireless, connect to an internet source:

```
sudo systemctl enable NetworkManager.service
sudo systemctl start NetworkManager.service
```

# connect to wireless
```
nmcli device wifi list
nmcli device wifi connect SSID password PASSWORD
```

Confirm access using `ping`.

## Set up Desktop Environment

Set up `xorg` by install the following packages:

  * xorg
  * xterm
  * xorg-xinit
  * xorg-xclock
  * xorg-twm

Test by starting `startx`.

Install the following packages to set up the window and display manager

  * awesome
  * lightdm
  * lightdm-gtk-greeter
  * (if desktop)
      * nvidia
      * nvidia-settings
      * pkg-config

Configure LightDM:

```
sudo systemctl enable lightdm.service
sudo systemctl start lightdm.service
```

You should now have a graphical display running.

## Configure Desktop Environment

First, set up `firefox` and sign in to sync all current changes.

### Set up Multi-Monitors

If using multiple monitors, set them up:

```sudo nvidia-xconfig```

Reboot computer, then run:

```sudo nvidia-settings```

Save settings to the X config file.

### Set up Dropbox

To configure the graphical system, get Dropbox synced to be able to use saved entries. First, install yay:

````
sudo pacman -S base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
````

Then import public key:

````
gpg --recv-keys --keyserver hkp://pgp.mit.edu:80 FC918B335044912E
````

Install Dropbox:

````
yay -S dropbox
````
Finally, start and sign in to Dropbox.

### Link Existing Configurations

Make symlinks for the following:

````
mkdir ~/.config/awesome
ln -s ~/Dropbox/config/rc.lua ~/.config/awesome/rc.lua
ln -s ~/Dropbox/config/Xresources-desktop ~/.Xresources
````

Reboot.

## Setting up Laptop

Install the following packages:

  * `cbatticon`
  * `acpilight`

To make backlight changes using keyboard shortcuts, edit the **/etc/rc.local** file.

```
chgrp -R backlighters /sys/class/backlight
chmod g+w /sys/class/backlight/*/*
```

Then add your user to the backlighters group:

```
sudo groupadd backlighters
sudo usermod -aG backlighters dfar
```

Restart and test the ability to inc/dec backlight with Fn+F11/F12

## Installing and Configure Software

### Essentials

Install `nautilus`, `flameshot`, `evince`, and `calc`.

Using yay, install `systemd-numlockontty`, and then enable numLockOnTty

`sudo systemctl enable numLockOnTty`

### Web Browsing/Mail/Chat

Install `qbittorrent`, `gnome-keyring`, and `libsecret`.

Install `mailspring` via yay, then add accounts, set to dark, and set signature.

Install `franz` via yay and sign in to all accounts, then turn on dark more and turn off todos.

### Sound/Video Management

Install `vlc`, `pulseaudio`, `pasystray`, and `pavucontrol`.

### Software Development

Install the following packages:

  * code
  * dotnet-sdk
  * otf-fira-code
  * nodejs
  * terraform
  * intellij-idea-community-edition
  * insomnia _(yay)_
  * azure-cli _(yay)_
  * azure-functions-core-tools-bin _(yay)_
  * azuredatastudio (yay)

Sync settings in VSCode.

### Networking

For VPN, install `openvpn`, `network-manager-applet`, and `networkmanager-openvpn`. Connect to all OpenVPN instances (log in using web and download profiles).

For RDP, install `remmina` and `freerdp`, then connect to all RDP boxes.

### Virtualization

Install `gnome-boxes`, then create a Win10 box with 8GB memory, 100GB HDD. Install the following software using chocolatey:

  * git
  * visualstudio2019community
  * sql-server-management-studio
  * sql-server-express

### Scanner/Printer

Install `sane` and `imagescan`. Then run `utsushi` for scanning.

For printing, install `cups`, `nss-mdns`, `avahi`, `python-gobject`, `pygtk`, `python-dbus`, and then enable and start the services:

```
sudo systemctl enable org.cups.cupsd.service
sudo systemctl start org.cups.cupsd.service
sudo systemctl start avahi-daemon.service
```

Edit `/etc/nsswitch.conf` and change the hosts line to include before `resolve` and `dns`:

```
mdns_minimal [NOTFOUND=return]
```

Now find the printer's IP address:

`avahi-discover`

And add the printer:

```
lpadmin -p OfficePrinter -E -v ipp://IP_ADDRESS/ipp/print -m everywhere
```

 [1]: https://dfar.io/installing-arch-linux/
