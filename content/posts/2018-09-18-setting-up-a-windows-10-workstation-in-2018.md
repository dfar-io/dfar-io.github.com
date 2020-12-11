---
title: Setting Up A Windows 10 Workstation in 2018
author: dfar

date: 2018-09-18T18:12:56+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-a-windows-10-workstation-in-2018/
categories:
  - Uncategorized

---
<!--[CDATA[<strong-->2018.10.24 EDIT: Added a few new applications when setting up my Surface Pro 4.

  
I recently built a new PC for myself to use for work and wanted to document the process of getting it set up for use. Each year, I go through and reinstall any system I have as a means to make sure I have this process down, alongside doing an audit to see what I&#8217;m actually using, to make sure I keep things clean.  
Here&#8217;s the process I use when setting up a Windows 10 workstation:

## Installation

First, I go to the Microsoft website and create a bootable thumb drive containing Windows 10.  
I restart my computer and boot to the thumb drive I created earlier. Once Windows setup loads, I end up selecting the option to **Custom: Install Windows only (advanced)**.  
For my PC, I have two drives running in it:

  * An SSD that&#8217;s responsible for storing the Windows 10 OS.
  * An HDD that&#8217;s responsible for holding all My Documents data, as well as everything for Dropbox.

I&#8217;ll select to install to OS on the SSD and kick off the installation process. After some time, the installation will finish and the computer will restart.

## Display Configuration

Once I have the Windows desktop running, I like to set up my&nbsp;display before I do anything else. I currently run 3 4K monitors with a GeForce 1080 Ti, so I will set up scaling and make sure the monitors are configured as desired.  
Once that&#8217;s done, I set up Windows to display everything as desired, including:

  * Shifting the Taskbar over to the right side, and only showing one taskbar.
  * Turn off Cortana, Tasks, and People
  * Changing to use Dark Mode for default apps

## Essentials: Mail

Once I have the display set up correctly, the next step for setting up a Windows 10 workstation is to set up some of the essential software on the system. I like to use the Windows 10 Mail app for both my mail and calendar. I&#8217;ll access that and add all relevant accounts, and then make the following configurations:

  * Creating a linked inbox
  * Turn off email notifications
  * Set Swipe Right to Delete
  * Create a signature for all accounts
  * Set the calendar to start on Monday

## Software Installation

Next up is downloading a host of software to use for the PC. I use [Chocolatey][1] for as much as I can, so after setting it up through Powershell (as administrator), I install the following applications using Chocolatey:

  * googlechrome&nbsp;_(sign in to account, configure extensions)_
  * divvy _(register application, start Divvy at login, hide from tray, set Ctrl-Shift-Z as_ shortcut_)_
  * 7zip
  * ccleaner&nbsp;_(set up to check automatically)_
  * dropbox _(log in and download all files, point to HDD)_
  * filezilla
  * git
  * nodejs
  * libreoffice-fresh
  * vscode
  * rescuetime (log in)
  * bitnami-xampp ([LAMP server instructions][2], including setting up a full local WordPress installation)
  * mysql.workbench
  * postman
  * qbittorrent
  * steam
  * sql-server-management-studio
  * teamviewer
  * visualstudio2017community
  * azure-cli

You can do this with one command, like so:

<pre class="lang:default decode:true ">choco install -y googlechrome ...</pre>

In addition, there are a few applications I use that aren&#8217;t in the Chocolatey package manager:

  * [Franz][3]&nbsp;&#8211; web application allowing me to combine Hangouts, Messenger and Slack into one application.
  * [Manta][4]&nbsp;&#8211; open source application I use for invoicing (looking for a replacement for this, one that can store data in the cloud).

After everything is installed, I make sure to go through each of the applications if they need configuring _(the notes are above for applications that need to be configured)_.

## Visual Studio Code Setup

Once all of my software is set up, I take a look at Visual Studio Code and set it up, since that&#8217;s where most of my development occurs.  
I install the following plugins:

  * TSLint
  * Angular Language Service
  * EditorConfig for VS Code
  * Path Intellisense
  * Prettier &#8211; Code formatter
  * Terraform
  * PowerShell

Once this is done, I install [Fira Code][5] as the font to be used.  
In terms of configuration, I copy and paste the current settings I like to use:

<pre class="lang:default decode:true">{
    "editor.rulers": [
        80
    ],
    "editor.minimap.enabled": false,
    "window.zoomLevel": 0,
    "explorer.confirmDelete": false,
    "explorer.confirmDragAndDrop": false,
    "workbench.startupEditor": "newUntitledFile",
    "editor.formatOnSave": true,
    "editor.fontFamily": "Fira Code",
    "editor.fontLigatures": true,
    "typescript.updateImportsOnFileMove.enabled": "always",
    "prettier.singleQuote": true,
    "prettier.printWidth": 80
}</pre>

## Configuring PowerShell

Once Visual Studio Code is set up, the next step is to configure Windows Powershell correctly. There are a few things to do here, build a profile, and then set up Azure CLI.  
I run the following commands in administrator PowerShell:

<pre class="lang:default decode:true">set-executionpolicy unrestricted
New-Item -path $profile -type file –force</pre>

Once that&#8217;s done, I should have a profile file created. I add the following to it:

<pre class="lang:default decode:true">set-location D:</pre>

Once that&#8217;s done, I&#8217;ll restart PowerShell to confirm that when it starts up, it&#8217;ll move to the D:.

## Final Touches

Once I&#8217;m all set with most things, there are a few more things I like to do:

  * Hide all desktop apps
  * Unpin everything from the Taskbar

## All Set And Ready To Go

Once all of that is done, we&#8217;re all set with setting up a Windows 10 workstation. I&#8217;ll take a look at this again in 2019 to see what changes in a year. Perhaps I&#8217;ll switch over to using Linux?]]>

 [1]: https://chocolatey.org/
 [2]: https://dfar.io/setting-up-a-local-wordpress-development-environment/
 [3]: https://meetfranz.com/
 [4]: https://github.com/hql287/Manta
 [5]: https://github.com/tonsky/FiraCode