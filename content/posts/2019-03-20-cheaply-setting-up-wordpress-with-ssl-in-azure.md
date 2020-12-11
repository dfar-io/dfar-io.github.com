---
title: Setting up WordPress in Azure with SSL for ~$8 a Month
author: dfar

date: 2019-03-21T00:53:15+00:00
excerpt: |
  
  <![CDATA[]]>
url: /cheaply-setting-up-wordpress-with-ssl-in-azure/
categories:
  - Uncategorized

---
<!--[CDATA[Setting up a LAMP server using a small virtual machine size is a good way to host low-traffic PHP sites (such as those running on WordPress) without having to use an expensive App Service. To give a perspective on the cost using App Services for a small WordPress site:

<ul-->

  * Basic plan (allowing for Always on and SSL): ~$50/month
  * MySQL for Azure: ~$25
With this solution, you will create everything on one virtual machine, allowing for dynamic scaling as needed for the machine. This does of course come with some downside:

  * You will need to handle backing up of both the files on the server and the data in the database.

## Creating the VM

First, create the VM and Resource Group:

  * RG name: <app>-<env>-<location>-rg
  * VM name:&nbsp;<app>-<env>-<location>-vm
  * Image: Ubuntu 18.04 LTS
  * VM size: B1s
  * VNet name:&nbsp;<app>-<env>-<location>-vnet
  * Diagnostics Storage Account: <app><env><location>vmdiag
  * Allow Inbound Port Access for HTTP, HTTPS, SSH
  * Login access through Azure Active Directory

Once the VM is created, access the NSG and add a restriction to IP to only allow your local IP to access:<figure class="wp-block-image">

<img class="wp-image-342" src="http://40.76.37.251/wp-content/uploads/2019/04/image.png" alt="" /> </figure> 

## Access VM and Install LAMP Server

Retrieve the public IP address and SSH into the server:

<pre class="wp-block-preformatted">ssh &lt;user&gt;@&lt;public_ip&gt;</pre>

Install LAMP Server:

<pre class="wp-block-preformatted">sudo apt update && sudo apt install lamp-server^</pre>

To ensure the installation happened successfully, run the following commands:

<pre class="wp-block-preformatted">apache2 -v
mysql -V
php -v</pre><figure class="wp-block-image">

<img class="wp-image-343" src="http://40.76.37.251/wp-content/uploads/2019/04/image-1.png" alt="" /> </figure> 

Once LAMP server is installed, verify that you can connect to HTTP using the public IP address &#8211;&nbsp;you should see the Apache2 Ubuntu Default Page:<figure class="wp-block-image">

<img class="wp-image-322" src="http://40.76.37.251/wp-content/uploads/2019/03/image-12.png" alt="" /> </figure> 

&nbsp;

## Set up MySQL

Once the web server is running, the next step is configuring MySQL. Run the following command, installing the Validate Password Plugin and using &#8220;Medium&#8221; policy:

<pre class="wp-block-preformatted">sudo mysql_secure_installation</pre>

When installing, use medium strength, and default yes to all options except &#8220;Disallow root login remotely?&#8221; [Generate a password][1].  
The next step is configuring access to MySQL through external servers (such as from a VPN). This assumes you&#8217;ll be using the NSG from Azure to restrict access based on desired IP addresses.  
Run a query to allow access:

<pre class="wp-block-preformatted">sudo mysql -u root -p
<code>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION; FLUSH PRIVILEGES;</code></pre>

Edit MySQL configuration:

<pre class="wp-block-preformatted">sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf</pre>

Comment out the line that says &#8216;bind-address&#8217;.  
After making that change, restart MySQL:

<pre class="wp-block-preformatted">sudo service mysql restart</pre>

Finally, create an NSG rule that allows for external access to port 3306:<figure class="wp-block-image">

<img class="wp-image-344" src="http://40.76.37.251/wp-content/uploads/2019/04/image-2.png" alt="" /> </figure> 

Once the installation is done, let&#8217;s verify that the MySQL server can be accessed. I usually use <a href="https://dev.mysql.com/downloads/workbench/" target="_blank" rel="noreferrer noopener">MySQL Workbench</a>&nbsp;and connect to the server using the following information:

  * Hostname: public IP

After MySQL is set up, set up any database that may be needed.  
Here, you&#8217;ll want to set up the WordPress database server, whether you are starting fresh or migrating from an old instance.  
If running into an issue with packet size, run the following command in MySQL and restart:

<pre class="wp-block-preformatted">sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf</pre>

The final step is creating a DB user specifically for the WordPress installation &#8211; this use would only have access to the specific database that WordPress uses. Create a user with the following:

  * Allow access to the DB used for the WordPress installation.

## Installing WordPress

After finishing setting up the LAMP server, next is installing WordPress. Assuming you&#8217;ve downloaded the source, you can use the following:

    scp -r .\wordpress-download\ vmadmin@YOUR_SERVER_IP:~

Now you&#8217;ll need to SSH into the server and move the files into `/var/www/html`:

    sudo mv -v ~/wordpress-download/* /var/www/html/

Before doing the 5-minute install, run the following to allow for Apache to have write permissions:  
Check the group that Apache is running under:

<pre>cat /etc/apache2/apache2.conf | grep ^Group -B 2
cat /etc/apache2/envvars | grep GROUP
# Give that group access
sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;
</pre>

Now perform the 5-minute install to ensure everything is working, you can access the site using the IP address to ensure everything in place.  
While doing this, make sure you can do the following:

  * Configure Apache to use `.htaccess` files. Change the following in `apache2.conf` to change `AllowOverride` to `All`: 
      * <pre>&lt;Directory /var/www/&gt;
  Options Indexes FollowSymLinks
  AllowOverride None
  Require all granted
&lt;/Directory&gt;</pre>

  * Access a page outside of the home page (`sudo a2enmod rewrite && sudo systemctl restart apache2`)
  * Upload a media file to test uploads

## Enabling Mail (using G Suite)

First, set up DNS to use email. For this example, we are using Google Suite for email. Add the following 5 MX records:

  * Host: @
  * Records (priority-URL) 
      * 1-ASPMX.L.GOOGLE.COM.
      * 5-ALT1.ASPMX.L.GOOGLE.COM.
      * 5-ALT2.ASPMX.L.GOOGLE.COM.
      * 10-ALT3.ASPMX.L.GOOGLE.COM.
      * 10-ALT4.ASPMX.L.GOOGLE.COM.

Then, do the following:  
https://wpforms.com/how-to-securely-send-wordpress-emails-using-gmail-smtp/

## Setting up SSL

Once the web server can be reached and LAMP is installed, the next step is securing the site using SSL. Run the following to enable SSL, enable the SSL site, and restart Apache:

<pre class="wp-block-preformatted">sudo a2enmod ssl
sudo a2ensite default-ssl
sudo systemctl reload apache2</pre>

Once that&#8217;s done, access the public IP using HTTPS &#8211; you should get an insecure cert warning.  
Now that we&#8217;ve determined the port is listening, let&#8217;s set up Let&#8217;s Encrypt. Using [CertBot][2]&nbsp;usually makes this much easier. Since in this case, we&#8217;re using Apache and Ubuntu 18.04, we just need to populate those values and run the commands provided by CertBot:<figure class="wp-block-image">

<img class="wp-image-345" src="http://40.76.37.251/wp-content/uploads/2019/04/image-3.png" alt="" /> </figure> 

With these commands, you&#8217;ll also need to set up DNS for the domain to use. With the public IP address, create the following:

  * create an A record with the host as @ and the IP address as the web server IP address.

After this finishes, run the CertBot job to create the certificate. After that finishes, allow for the ability to redirect to HTTPS using the CertBot plugin.  
Reference:

  * <https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-lamp-stack>

]]>

 [1]: https://passwordsgenerator.net/
 [2]: https://certbot.eff.org/