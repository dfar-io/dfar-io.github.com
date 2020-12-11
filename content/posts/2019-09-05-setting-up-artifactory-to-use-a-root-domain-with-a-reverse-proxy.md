---
title: Setting up Artifactory to use a root domain with a Reverse Proxy
author: dfar

date: 2019-09-05T17:39:08+00:00
url: /setting-up-artifactory-to-use-a-root-domain-with-a-reverse-proxy/
categories:
  - Uncategorized

---
When installing Artifactory, you&#8217;ll be able to start using it as \`http://YOUR_DOMAIN:8081/artifactory. To change this to a simpler domain, you can read the following guide. You&#8217;ll need to have the following:

  * Artifactory administrative access
  * SSH access to the server.

## Setting up Reverse Proxy to Simplify Domain

First, SSH into the server and install Apache:

<pre class="wp-block-code"><code>sudo apt install apache2 -y</code></pre>

This should create a default page you can access by hitting the root URL:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-3-1024x172.png" alt="" class="wp-image-615" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-3-1024x172.png 1024w, https://40.76.37.251/wp-content/uploads/2019/09/image-3-300x50.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-3-768x129.png 768w, https://40.76.37.251/wp-content/uploads/2019/09/image-3.png 1263w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Now configure the appropriate modules:

`sudo a2enmod proxy_http proxy_ajp rewrite deflate headers proxy_balancer proxy_connect proxy_html ssl lbmethod_byrequests slotmem_shm proxy`

While logged in as an admin in Artifactory, access `Admin -> HTTP Settings` and make the following changes:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-4-1024x451.png" alt="" class="wp-image-616" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-4-1024x451.png 1024w, https://40.76.37.251/wp-content/uploads/2019/09/image-4-300x132.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-4-768x338.png 768w, https://40.76.37.251/wp-content/uploads/2019/09/image-4.png 1231w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

After saving, you&#8217;ll see the ability to View/Download the configuration on the right side of the page. Download the contents and replace the contents of `/etc/apache2/sites-enabled/000-default.conf` on the Artifactory server with them.

Restart Apache with `sudo systemctl restart apache2`.

Now confirm that you can request Artifactory using http://YOUR_DOMAIN.

## Setting up SSL using Let&#8217;s Encrypt

Now that you can access the domain, let&#8217;s secure the domain using Let&#8217;s Encrypt.

Use the following to check which version of Linux you&#8217;re running: `<code>lsb_release -a`</code>

Most likely, you&#8217;ll be using Ubuntu 18.04, if so, you can use <a rel="noreferrer noopener" aria-label="these steps (opens in a new tab)" href="https://certbot.eff.org/lets-encrypt/ubuntubionic-apache" target="_blank">these steps</a>. If not, check out the <a rel="noreferrer noopener" aria-label="CertBot  (opens in a new tab)" href="https://certbot.eff.org" target="_blank">CertBot </a>page for appropriate directions.

You should be able to generate and configure the cert automatically running the directions above. Assuming this is successful, select to **redirect HTTP traffic to HTTPS, removing HTTP access.**

Now confirm that you can both:

  1. Access the HTTPS version of your domain above.
  2. When trying to access HTTP, it automatically redirects to HTTPS.

Reference: <https://www.jfrog.com/confluence/display/RTF/Configuring+Apache>