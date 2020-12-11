---
title: Setting up Jenkins in Azure
author: dfar

date: 2019-07-08T08:08:32+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-jenkins-in-azure/
categories:
  - Uncategorized

---
Before getting started, you&#8217;ll need to have:

  * An Azure tenant and subscription.
  * OpenSSH ([installation][1] for Windows 10)

## Installing Jenkins via Azure Marketplace

The easiest way to install Jenkins is to use the [Azure Marketplace][2] link. A couple suggestions when setting up:

  * I recommend using an SSH Public Key to sign in. If you haven&#8217;t yet, generate one using `ssh-keygen` and then get it using `cat ~.ssh\id_rsa.pub`
  * Set up a domain name label, especially if you aren&#8217;t planning to put this behind a different domain.
  * Set the VM as B1ms starting off &#8211; you can upgrade later as the system is used more.

After creation, modify the NSG created and use your public IP to secure SSH access (<a href="http://ip4.me/" target="_blank" rel="noreferrer noopener" aria-label="check your public IP (opens in a new tab)">check your public IP</a>).

Next, SSH into the server using the IP and check to see if you can <a rel="noreferrer noopener" href="https://dfar.io/upgrading-ubuntu/" target="_blank">update the OS</a> (as of this writing, the image ships with Ubuntu 16.04 LTS, and can be upgraded to 18.04 LTS). 

## Connecting a Domain

If you&#8217;re planning to use a different domain to host Jenkins (as opposed to the provided _domain-name-label_._location_.cloudapp.azure.com), set the following DNS record:

  * CNAME
  * Host: desired subdomain (ex. jenkins -> jenkins.dfar.io)
  * Value: DNS record from Azure.

Since DNS will take a second, check to verify you can access the new server.

## Setting up SSL using Let&#8217;s Encrypt

The next step is setting up SSL using Let&#8217;s Encrypt to allow for an HTTPS connection. First, open the 443 port on the VM:

<pre class="wp-block-code"><code>az network nsg rule update -g RG_NAME --nsg-name NSG_NAME -n http-rule --destination-port-ranges 80, 443</code></pre>

Now SSH into the server and modify SSL Offloading:

<pre class="wp-block-code"><code>sudo nano /etc/nginx/sites-available/default</code></pre>

Use the following configuration:

<pre class="wp-block-code"><code>server {
    listen 80 default_server;
    server_name _;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name CUSTOMDOMAIN;
    ssl_certificate /etc/letsencrypt/live/CUSTOMDOMAIN/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/CUSTOMDOMAIN/privkey.pem;
    location / {
        proxy_set_header        Host $host:$server_port;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Proto $scheme;


        # Fix the “It appears that your reverse proxy set up is broken" error.
        proxy_pass          http://localhost:8080;
        proxy_read_timeout  90;
    }
}</code></pre>

Then run the following commands:

<pre class="wp-block-code"><code>sudo service nginx stop
git clone https://github.com/letsencrypt/letsencrypt
./letsencrypt/letsencrypt-auto certonly
sudo service nginx restart</code></pre>

## Accessing and Logging Into Jenkins

After completed, access the Jenkins instance at your domain. Verify that both the SSL connection is valid and that you are on the &#8216;Unlock Jenkins&#8217; page:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-2-1024x315.png" alt="" class="wp-image-480" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-2-1024x315.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-2-300x92.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-2-768x236.png 768w, https://40.76.37.251/wp-content/uploads/2019/07/image-2-1568x482.png 1568w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Run the following command in the SSHed server to get a code for the screen:

<pre class="wp-block-preformatted">sudo cat /var/lib/jenkins/secrets/initialAdminPassword</pre>

Next, you&#8217;ll get a request to either install suggested plugins or select plugins as desired. **I recommend going through and selecting the plugins desired to keep the installation minimal**. Remove anything from the list that you may not need (such as Subversion). You can always add plugins later if you find you need.

After that, create an admin user for yourself, and you&#8217;ll be ready to get started!

## Next Steps

After you&#8217;ve finished setting up Jenkins, a few next steps would be:

  * Setting up a CI/CD job
  * [Setting up Azure AD Authentication][3]

## Reference<figure class="wp-block-embed-wordpress wp-block-embed is-type-wp-embed is-provider-wu-shuai-039-s-blog">

<div class="wp-block-embed__wrapper">
  https://blogs.msdn.microsoft.com/wushuai/2018/04/09/support-https-in-azure-marketplace-image-for-jenkins/
</div></figure>

 [1]: https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse
 [2]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins
 [3]: https://dfar.io/setting-up-jenkins-with-azure-ad-authentication/