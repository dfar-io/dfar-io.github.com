---
title: Setting Up Ubuntu
author: dfar

date: 2020-02-05T15:34:24+00:00
url: /setting-up-ubuntu/
categories:
  - Technology
  - Uncategorized

---
Recently switched over to using Ubuntu from Windows due to having some issues with running Windows 10 on the desktop. Here&#8217;s a quick guide on what I did to get everything set up.

## Installation

Pretty simple, just set up a USB stick with Ubuntu on it. When install, using the following configurations:

  * Minimal installation
  * Install third party tools (save time later)

Once the installation completes, I make sure to set up the display correctly for monitors, including:

  * Setting scale to %200
  * Turning on the night light

And then a few steps before installing software:

  * Set up Livepatch (security updates)
  * Unpin favorites
  * Signing into Firefox with my Firefox account.
  * Move Dock to the bottom.

## Software

I install the following:

  * Dropbox
  * Franz (login, set dark mode, turn off todos, notifications)
  * Mailspring (dark theme, add accounts, turn notifications off)
  * VSCode (download FiraCode, sync settings)
  * PyGrid (set xdivs to 3, automatic startup)
  * <a rel="noreferrer noopener" aria-label="Azure CLI (opens in a new tab)" href="https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest" target="_blank">Azure CLI</a>
  * MySQL Workbench
  * OpenVPN Connect (_sudo apt-get install openvpn,_ set up server profiles)
  * Remmina (for remoting into Windows PCs)
  * <a rel="noreferrer noopener" aria-label="Terraform (opens in a new tab)" href="https://askubuntu.com/questions/983351/how-to-install-terraform-in-ubuntu" target="_blank">Terraform</a>
  * kubectl
  * Gnome Tweak Tools (animations off, set dark theme, application menu off, 1 static workspace)
  * .NET Core SDK
      * Azure Functions Core Tools
  * VirtualBox (with a Windows 10 VM with 8GB memory, 100GB hard drive space) with the following installed:
      * VS2019
      * SSMS
      * SQL Server Express
      * Dropbox
      * Git