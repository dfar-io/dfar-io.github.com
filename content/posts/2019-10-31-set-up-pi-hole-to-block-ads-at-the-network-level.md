---
title: Set up Pi-Hole to Block Ads at the Network Level
author: dfar

date: 2019-10-31T19:45:47+00:00
url: /set-up-pi-hole-to-block-ads-at-the-network-level/
categories:
  - Uncategorized

---
You can use a Respberry Pi to block all ads coming in at a DNS level

Requirements:

  * <a href="https://www.microcenter.com/product/510566/canakit-complete-starter-kit-for-raspberry-pi-3-model-b---32gb-evo-edition" target="_blank" rel="noreferrer noopener" aria-label="CanaKit  (opens in a new tab)">CanaKit </a>(provides Raspberry Pi, Power Supply)
  * Short ethernet cable
  * Monitor and keyboard for initial setup

## Raspberry Pi Initial Installation and Configuration

Assemble and then plug in the Raspberry Pi, which should take you to the NOOBS setup window.

Install Raspbian, and work through until you get to the desktop screen for the Raspberry Pi.

## Router Configuration

With the Raspberry Pi configured, connect to your router admin page and find the IP address of the Raspberry Pi. Assign a static IP to the Raspberry Pi.

## Pi-Hole Installation

On the Raspberry Pi, run the following commands to start installation:

<pre class="wp-block-code"><code>wget -O basic-install.sh https://install.pi-hole.net
sudo bash basic-install.sh</code></pre>

When asked for Upstream DNS Provider, select Cloudflare.

For everything else, just select the default options.

After installation finishes, you&#8217;ll be able to log into the web admin with the password provided at the end of installation.

You may want to change the password used to login, you can run the following command on the Raspberry Pi:

<pre class="wp-block-code"><code>pihole -a -p</code></pre>

## Pi-Hole Configuration

Access the web interface using http://<IP_ADDRESS>/admin, and log in using the password above.

Change the DNS server on your router to the IP address above.

## Verification

An easy way to verify is to check a page that uses ads (<a rel="noreferrer noopener" aria-label="here's a good example (opens in a new tab)" href="https://www.onegreenplanet.org/news/this-dog-looks-like-hes-dancing-but-heres-why-you-might-want-to-be-concerned/" target="_blank">here&#8217;s a good example</a>). See if any ads appear, and then check the Pi-Hole admin:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-11-1024x139.png" alt="" class="wp-image-720" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-11-1024x139.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-11-300x41.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-11-768x104.png 768w, https://40.76.37.251/wp-content/uploads/2019/10/image-11.png 1441w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Reference<figure class="wp-block-embed-wordpress wp-block-embed is-type-wp-embed is-provider-smarthomebeginner">

<div class="wp-block-embed__wrapper">
  <blockquote class="wp-embedded-content" data-secret="cOobig56Xu">
    <a href="https://www.smarthomebeginner.com/pi-hole-setup-guide/">Complete Pi Hole setup guide: Ad-free better internet in 15 minutes</a>
  </blockquote>
</div></figure> 

<https://kb.netgear.com/25722/How-do-I-reserve-an-IP-address-on-my-NETGEAR-router>

<https://cleanbrowsing.org/guides/cleanbrowsing-netgear-orbi-dns-configuration>