---
title: Configuring Arch Linux
author: dfar

date: 2020-02-09T02:25:02+00:00
url: /configuring-arch-linux/
categories:
  - Technology
  - Uncategorized

---
If you&#8217;re [installed Arch Linux][1], the next step is configuring the system to work as desired. In this guide, I walk through my general configuration of a newly installed Arch Linux instance.

## Configure Internet 

**S**et up **networkmanager** and if wireless, connect to an internet source:

<pre class="wp-block-code"><code>sudo systemctl enable NetworkManager.service
sudo systemctl start NetworkManager.service

# connect to wireless
nmcli device wifi list
nmcli device wifi connect SSID password PASSWORD</code></pre>

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

<pre class="wp-block-code"><code>sudo systemctl enable lightdm.service
sudo systemctl start lightdm.service</code></pre>

You should now have a graphical display running.

## Configure Desktop Environment

First, set up `firefox` and sign in to sync all current changes.

### Set up Multi-Monitors

If using multiple monitors, set them up:

<pre class="wp-block-code"><code>sudo nvidia-xconfig</code></pre>

Reboot computer, then run:

<pre class="wp-block-code"><code>sudo nvidia-settings</code></pre>

Save settings to the X config file.

### Set up Dropbox

To configure the graphical system, get Dropbox synced to be able to use saved entries. First, install yay:

<pre class="wp-block-code"><code>sudo pacman -S base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si</code></pre>

Then import public key:

<pre class="wp-block-code"><code>gpg --recv-keys --keyserver hkp://pgp.mit.edu:80 FC918B335044912E</code></pre>

Install Dropbox:

<pre class="wp-block-code"><code>yay -S dropbox</code></pre>

Finally, start and sign in to Dropbox.

### Link Existing Configurations

Make symlinks for the following:

<pre class="wp-block-code"><code>mkdir ~/.config/awesome
ln -s ~/Dropbox/config/rc.lua ~/.config/awesome/rc.lua
ln -s ~/Dropbox/config/Xresources-desktop ~/.Xresources</code></pre>

Reboot.

## Setting up Laptop

Install the following packages:

  * `cbatticon`
  * `acpilight`

To make backlight changes using keyboard shortcuts, edit the **/etc/rc.local** file.

<pre class="wp-block-code"><code>chgrp -R backlighters /sys/class/backlight
chmod g+w /sys/class/backlight/*/*</code></pre>

Then add your user to the &#8216;backlighters&#8217; group:

<pre class="wp-block-code"><code>sudo groupadd backlighters
sudo usermod -aG backlighters dfar</code></pre>

Restart and test the ability to inc/dec backlight with Fn+F11/F12

## Installing and Configure Software

### Essentials

Install `nautilus`, `flameshot`, `evince`, and `calc`.

Using yay, install `systemd-numlockontty`, and then enable numLockOnTty

<pre class="wp-block-code"><code>sudo systemctl enable numLockOnTty</code></pre>

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

<pre class="wp-block-code"><code>sudo systemctl enable org.cups.cupsd.service
sudo systemctl start org.cups.cupsd.service
sudo systemctl start avahi-daemon.service</code></pre>

Edit `/etc/nsswitch.conf` and change the hosts line to include before `resolve` and `dns`:

<pre class="wp-block-code"><code>mdns_minimal [NOTFOUND=return]</code></pre>

Now find the printer&#8217;s IP address:

<pre class="wp-block-code"><code>avahi-discover</code></pre>

And add the printer:

<pre class="wp-block-code"><code>lpadmin -p OfficePrinter -E -v ipp://IP_ADDRESS/ipp/print -m everywhere</code></pre>

 [1]: https://dfar.io/installing-arch-linux/