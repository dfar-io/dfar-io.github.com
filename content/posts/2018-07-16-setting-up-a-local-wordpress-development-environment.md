---
title: Setting Up a Local WordPress Development Environment
author: dfar

date: 2018-07-16T16:00:09+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-a-local-wordpress-development-environment/
categories:
  - Uncategorized

---
<!--[CDATA[When developing on WordPress, consider setting up a local WordPress development environment to allow for freedom to experiment with the installation.  This will assist a series of tasks including:

<ul-->

  * Setting up the website and adding content.
  * Installing plugins to test their functionality.
  * Viewing a functional website without a shared environment.
  * Theme or plugin development.
This guide will walk you through the following steps.

  1. Installing a LAMP Development Environment
  2. Creating a database
  3. Downloading WordPress core files, and setting them up on server.
  4. Creating a local domain for use.

## Installing A LAMP Development Environment

<a href="https://www.apachefriends.org/index.html" target="_blank" rel="noopener noreferrer">XAMPP</a> is a cross-platform LAMP (Linux, Apache, MySQL, PHP) environment used for running WordPress on your local machine.&nbsp; A few platform-specific alternatives to use as well are&nbsp;<a href="http://www.wampserver.com/en/" target="_blank" rel="noopener noreferrer">WampServer</a> for Windows, and <a href="https://www.mamp.info/en/" target="_blank" rel="noopener noreferrer">MampServer</a> for OS X.&nbsp; All of these options will provide the same functionality for running a local WordPress environment, but I chose XAMPP for the following reasons:

  * Cross-platform compatibility, the setup and configuration for this tool will stay the same between different platforms.
  * Personal preference for the XAMPP Control Panel over the other tools_._
  * The use of MariaDB over MySQL**.

_**Note that XAMPP actually uses MariaDB as it&#8217;s database server.&nbsp; This is different than the standard MySQL database server used for WordPress.&nbsp; &nbsp;I&#8217;ve provided a reference to a few articles at the end of this post if you&#8217;re interested in learning more._

## Starting with XAMPP

When opening XAMPP, you&#8217;ll be presented with the Control Panel:  
To get the environment started, start both the Apache and MySQL services.&nbsp; Check that the server is running correctly by accessing http://localhost on your machine.&nbsp; If the XAMPP dashboard appears, you&#8217;re good to move to the next step.

## Creating a Database

The next step is creating or importing a database in your local MySQL server.&nbsp; This can be done using phpMyAdmin (set up with your XAMPP installation) or by using <a href="https://www.mysql.com/products/workbench/" target="_blank" rel="noopener noreferrer">MySQL Workbench</a>&nbsp;to connect.&nbsp; I&#8217;ll use MySQL Workbench, since it gives a more friendly UI to work with.  
Right-click &#8216;Schemas&#8217; on the left side to create a database for WordPress to use.&nbsp; Later, you&#8217;ll use this schema name when running the WordPress installer.

## Linking/Installing WordPress

After the server is running and the database created, the next step is to get the WordPress source code onto the server.&nbsp; <a href="https://wordpress.org/download/" target="_blank" rel="noopener noreferrer">Download</a> the files for WordPress and copy them to a directory on your computer, something like:

<pre class="">C:\code\&lt;your-project-name&gt;</pre>

You&#8217;ll likely want to store the source code for the site somewhere other than the /htdocs folder.&nbsp; This way, you can have a central repository on your machine for all projects.&nbsp; You can use a <a href="https://en.wikipedia.org/wiki/Symbolic_link" target="_blank" rel="noopener noreferrer">symlink</a>&nbsp;to accomplish this.&nbsp; Open a command line terminal as an admin and run the following:

<pre class="">mklink /D C:\xampp\htdocs\&lt;your-project-name&gt; C:\code\&lt;your-project-name&gt;</pre>

After this, check that you can access the WordPress installation at **http://localhost/<your-project-name>.&nbsp;&nbsp;**Assuming you can see the WordPress installation screen, you have everything ready to get started.&nbsp; You can progress from here if you so desire, but let&#8217;s also create a local domain for use as well, to make access to the site a little easier.

## Creating a Local Domain

Creating a local domain will allow you to access your local running website at something such as **http://local.<your-project-name>.com**.&nbsp; There are two parts:

  1. Editing the Apache server configuration to serve the local domain.
  2. Editing the hosts file to point the new domain from your machine to your server.

First, open the Apache Virtual Hosts configuration file at&nbsp;C:\xampp\apache\conf\extra\httpd-vhosts.conf and add the following:

<pre class="">&lt;VirtualHost *:80&gt;
 DocumentRoot "C:\xampp\htdocs\&lt;your-project-name&gt;"
 ServerName local.&lt;your-project-name&gt;.com
 ErrorLog "logs/&lt;your-project-name&gt;.error.log"
 CustomLog "logs/&lt;your-project-name&gt;.custom.log"
&lt;/VirtualHost&gt;</pre>

Next, open the&nbsp;C:\Windows\System32\drivers\etc\hosts **as an administrator** and add the following:

<pre class="">127.0.0.1 &lt;your-local-domain&gt;</pre>

Restart Apache using XAMPP Control Panel.&nbsp; Attempt to access the site using the local domain.&nbsp; If you&#8217;re able to access the WordPress installer screen at the local domain, you&#8217;ll all set to start the 5-minute installation.

## Complete the 5-minute WordPress Installation

Installing WordPress is very simple.&nbsp; Select the language for installation, and then fill out the database connection details like below.&nbsp; You&#8217;ll just need to change the database name to the schema created:  
You should now be all set with a local WordPress environment, useful for plugin development, trying out different themes, and other configuration changes without affecting a live website.

## References

<a href="https://www.infoworld.com/article/2611812/mysql/mysql-face-off--mysql-or-mariadb-.html" target="_blank" rel="noopener noreferrer">MySQL Face-off:&nbsp; &nbsp;MySQL or MariaDB?</a>]]>