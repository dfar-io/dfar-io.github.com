---
title: Set up nopCommerce 4.2 on a Linux Azure VM
author: dfar

date: 2020-03-09T18:50:55+00:00
url: /set-up-nopcommerce-4-2-on-a-linux-azure-vm/
categories:
  - Technology

---
## Infrastructure Setup

First, create a Ubuntu-based VM, opening ports 80, 443, and 22.

Afterwards, create an Azure SQL Database.

## VM Configuration

Once all of the infrastructure is created, set up a DNS record for the public IP created for the VM to make access easier.

Next, SSH into the VM using the domain name created above. Run the following commands to install the .NET Core runtime and SDK:

<pre class="wp-block-code"><code>wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb

sudo add-apt-repository universe
sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install aspnetcore-runtime-2.2 dotnet-sdk-2.2</code></pre>

Next, install and enable nginx:

<pre class="wp-block-code"><code>sudo apt-get install nginx
sudo systemctl enable nginx
sudo systemctl start nginx</code></pre>

Edit `/etc/nginx/sites-available/default`:

<pre class="wp-block-code"><code>server {
    listen 80 default_server;
    listen [::]:80 default_server;

    server_name   YOUR_DOMAIN.com;

    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}</code></pre>

Finally, restart NGINX:

<pre class="wp-block-code"><code>sudo systemctl restart nginx</code></pre>

## Set up NopCommerce

This step will lay out the process of downloading the NoSource version of nopCommerce and getting it running.

If you have a custom copy of nopCommerce from source, you would publish it and use the output here instead.

If starting from scratch, run the following:

<pre class="wp-block-code"><code>sudo wget https://github.com/nopSolutions/nopCommerce/releases/download/release-4.20/nopCommerce_4.20_Source.rar

sudo apt-get install unrar -y
mkdir nopSource
unrar x nopCommerce_4.20_Source.rar nopSource

cd nopSource/src
dotnet clean -c Release
dotnet build -c Release

cd Presentation/Nop.Web
sudo mkdir /var/www/nop
sudo dotnet publish -c Release -o /var/www/nop

cd /var/www/nop
sudo mkdir bin
sudo mkdir logs

cd ..

sudo chown -R www-data:www-data nop/</code></pre>

Now create the nopCommerce service by creating file `/etc/systemd/system/nop.service`:

<pre class="wp-block-code"><code>[Unit]
Description=nopCommerce

[Service]
WorkingDirectory=/var/www/nop
ExecStart=/usr/bin/dotnet /var/www/nop/Nop.Web.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=nop
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target</code></pre>

Enable the above service:

<pre class="wp-block-code"><code>sudo systemctl enable nop.service
sudo systemctl start nop.service</code></pre>

Once this is all set, you should be able to install the store, using the Azure SQL Database created above. When installing, you may want to use the connection string provided by Azure:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/03/image-16-1024x391.png" alt="" class="wp-image-1124" srcset="https://40.76.37.251/wp-content/uploads/2020/03/image-16-1024x391.png 1024w, https://40.76.37.251/wp-content/uploads/2020/03/image-16-300x115.png 300w, https://40.76.37.251/wp-content/uploads/2020/03/image-16-768x293.png 768w, https://40.76.37.251/wp-content/uploads/2020/03/image-16-1536x587.png 1536w, https://40.76.37.251/wp-content/uploads/2020/03/image-16-2048x782.png 2048w, https://40.76.37.251/wp-content/uploads/2020/03/image-16-1200x458.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

While installing, you&#8217;ll likely see a 504 timeout from NGINX &#8211; this is okay, just give some time for the installation to finish, and you should be able to eventually see the store front. 

## Set up SSL using Let&#8217;s Encrypt

To set up Let&#8217;s Encrypt, follow the [Certbot][1] directions, making sure **to enable auto-redirect.**

Afterwards, make the following change to the **appsettings.json** file:

<pre class="wp-block-code"><code>"Hosting": {
    //Set to "true" the settings below if your hosting uses a load balancer$
    "UseHttpClusterHttps": false,
    "UseHttpXForwardedProto": true,</code></pre>

Restart the NopCommerce service and ensure you can view the front page without an SSL warning.

 [1]: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx