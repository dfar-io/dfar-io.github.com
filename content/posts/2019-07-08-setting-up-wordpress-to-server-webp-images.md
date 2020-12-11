---
title: Setting up WordPress to serve next-gen WebP images
author: dfar

date: 2019-07-08T13:14:55+00:00
url: /setting-up-wordpress-to-server-webp-images/
categories:
  - Uncategorized

---
 

## Why do this?

If you are looking to improve performance on your website, this will automatically convert the images on your site to the more efficient WebP format. This will help with Google Lighthouse scores (especially in solving the &#8220;Serve images in next-gen format&#8221; issue).

## Procedure

Install the [WebP Express Plugin][1] (and buy the developer a coffee!)

Go into the WebP Express settings, accept the defaults, and click &#8216;Save settings&#8217;.

Verify this is working by running Google&#8217;s Lighthouse analytics. You should not see the item &#8216;Serve images in next-gen formats&#8221; item in your listing.

## Installing Dependencies

Depending on the server configuration you have, you may need to do more work after installing the WebP Express Plugin. If you see the following, you&#8217;ll need to configure your server to allow access:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-1.png" alt="" class="wp-image-472" /> </figure> 

SSH into the server and install the `gd` using the following:

<pre class="wp-block-code"><code>sudo apt-get install php7.2-gd</code></pre>

In addition, you may need to install mod_headers:

<pre class="wp-block-code"><code>sudo a2enmod headers
sudo systemctl restart apache2</code></pre>

 [1]: https://wordpress.org/plugins/webp-express/