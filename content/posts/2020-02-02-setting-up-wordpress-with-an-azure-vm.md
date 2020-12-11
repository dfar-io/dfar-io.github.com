---
title: Setting Up WordPress With An Azure VM
author: dfar

date: 2020-02-02T02:01:29+00:00
url: /setting-up-wordpress-with-an-azure-vm/
categories:
  - Technology
  - Uncategorized

---
This guide will walk though setting up a WordPress site using an Azure VM. Some of the perks of doing this are:

  * Cost-wise, you can spend as little as ~$8 a month to get a fully functional WordPress site running. If you try using MySQL and App Services in Azure, you&#8217;ll looking to spend ~$40 a month for worse specs.
  * You can scale the site up as needed if performance demands arise.

## Procedure

### Step 1: Create An Azure VM

First, create an Azure VM with the following specs:

  * Image of Ubuntu 18.04 LTS
  * Size of B1s (anything less and you&#8217;ll run into issues)
  * Azure Backup (optional, provides a means to restore VM from scratch)

Once the VM is set, set the following Network Security Group rules:

  * Public Access &#8211; ports 80/443
  * Private Access (such as your IP address) &#8211; ports 22/3306

### Step 2: Set up LAMP Server

SSH into the server and install a LAMP server with the following:

<pre class="wp-block-code"><code>sudo apt update && sudo apt -y install lamp-server^ php-curl php-dom php-mbstring php-imagick php-zip</code></pre>

After installation, verify correct installation:

<pre class="wp-block-code"><code>apache2 -v && mysql -V && php -v</code></pre>

### Step 3: Configure MySQL

Run the following:

<pre class="wp-block-code"><code>sudo mysql_secure_installation</code></pre>

Do the following:

  * Install the Validate Password Plugin at &#8220;Medium&#8221;
  * Say yes to all options except &#8220;Disallow root login remotely&#8221;

Run a query to allow access:

<pre class="wp-block-code"><code>sudo mysql -u root -p

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'GENERATED_PASSWORD' WITH GRANT OPTION; FLUSH PRIVILEGES;

exit</code></pre>

Edit the file **/etc/mysql/mysql.conf.d/mysqld.cnf** and comment out the **bind-address** line.

Restart MySQL:

<pre class="wp-block-code"><code>sudo service mysql restart</code></pre>

Finally, test connecting to the database server using a tool like <a rel="noreferrer noopener" aria-label="MySQL Workbench (opens in a new tab)" href="https://www.mysql.com/products/workbench/" target="_blank">MySQL Workbench</a>. When connected to the database server, do the following:

  * Create a DB (either copied from a migration or a fresh database)
  * Create a user that has access to the DB above.

### Step 4: Install WordPress

Download and unzip WordPress into **/var/www/html**:

<pre class="wp-block-code"><code>wget https://wordpress.org/latest.tar.gz
sudo tar -xzf latest.tar.gz -C /var/www/html --strip-components=1</code></pre>

Configure **/etc/apache2/apache2.conf** and allow AllowOverride to All in the **/var/www** directory.

Set the correct permissions for Apache:

<pre class="wp-block-code"><code>sudo chown -R www-data:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod 755 {} \;
sudo find /var/www/html -type f -exec chmod 644 {} \;

sudo a2enmod rewrite && sudo systemctl restart apache2</code></pre>

As a final step, delete the **index.html** file from Apache:

<pre class="wp-block-code"><code>sudo rm /var/www/html/index.html</code></pre>

You should now be able to run the WordPress installer and connect the database.

Once the WordPress Installer has run, log into the backend and check the site health to see if there are any misconfigurations within the system:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/02/image-3-1024x390.png" alt="" class="wp-image-1023" srcset="https://40.76.37.251/wp-content/uploads/2020/02/image-3-1024x390.png 1024w, https://40.76.37.251/wp-content/uploads/2020/02/image-3-300x114.png 300w, https://40.76.37.251/wp-content/uploads/2020/02/image-3-768x293.png 768w, https://40.76.37.251/wp-content/uploads/2020/02/image-3-1536x585.png 1536w, https://40.76.37.251/wp-content/uploads/2020/02/image-3-2048x780.png 2048w, https://40.76.37.251/wp-content/uploads/2020/02/image-3-1200x457.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Optional Steps

### Setting Up SSL With Let&#8217;s Encrypt

Before setting up SSL, you&#8217;ll need to set up a domain and set the appropriate DNS settings to be able to run the Let&#8217;s Encrypt process.

Enable SSL and restart Apache:

<pre class="wp-block-code"><code>sudo a2enmod ssl && sudo a2ensite default-ssl && sudo systemctl reload apache2</code></pre>

Run the commands provided by <a rel="noreferrer noopener" aria-label="CertBot (opens in a new tab)" href="https://certbot.eff.org/lets-encrypt/ubuntubionic-apache" target="_blank">CertBot</a>.

### Enable Mail

The easiest way to enable mail is to use the WP Mail SMTP plugin. Once configured, you can use the plugin to set up alongside the email carrier of choice and test the submission functionality.

### Increase Upload Size

To increase upload size (for things like uploading themes/plugins/media), edit the **/etc/php/7.2/apache2/php.ini** file:

<pre class="wp-block-code"><code>upload_max_filesize = 16M
post_max_size = 16M</code></pre>

Then restart Apache:

<pre class="wp-block-code"><code>sudo service apache2 restart</code></pre>

### Restore from a Backup

If opted into backup when creating the VM, the VM will have an automatic daily backup progress that you can use to restore the VM at any point.

To restore a new VM, access the Recovery Vault and go to Backup Items -> Your VM Name -> Restore VM. Once you go this, you have two options:

  * Completely create a new VM with the data restored.
  * Restore the disks into your current VM (VM must be turned off to do this).