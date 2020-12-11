---
title: Setting up A Jitsi Meet Server on Azure
author: dfar

date: 2020-04-11T19:01:39+00:00
url: /setting-up-a-jitsi-meet-server-on-azure/
categories:
  - Technology

---
With the COVID-19 pandemic going on, video chat is on the rise, with people using tools like Zoom, WebEx, and others. One option is to use an open-source solution like [Jitsi Meet][1], which provides both a [public cloud version you can use easily][1], and a version you can host on your own.

Why host on your own? You should consider hosting on your own if:

  * You&#8217;re concerned with security &#8211; hosting the software on your own ensures a third part is uninvolved.
  * You want more hands on capability to change the specs on the server for performance based on when you&#8217;re using video software.
  * You can control the location of the hardware, which depending on where everyone is located, may improve performance.

## Server Creation and Jitsi Installation

First, create an Azure VM with the following:

  * Running Ubuntu
  * Open inbound ports 80, 443, and 22.
  * SSH key access _(recommended)_

Once the VM is created, SSH into the server and install the Jitsi full suite:

<pre class="wp-block-code"><code>wget -qO - https://download.jitsi.org/jitsi-key.gpg.key | sudo apt-key add -

sudo sh -c "echo 'deb https://download.jitsi.org stable/' > /etc/apt/sources.list.d/jitsi-stable.list"

sudo apt-get -y update

sudo apt-get -y install jitsi-meet</code></pre>

when installing, you&#8217;ll be asked to configure `jitsi-videobridge2`, use the URL you plan to use for this Jitsi instance. Afterwards, generate a self-signed certificate _(we&#8217;ll create one with Let&#8217;s Encrypt later)_.

To verify installation worked successfully, visit the IP address in a browser, **making sure you use HTTPS**.

## Set up SSL with Let&#8217;s Encrypt

To set up SSL, you&#8217;ll need to set up a domain name for the server in place &#8211; you can either:

  * Configure the Public IP assigned by Azure to create a domain name.
  * Using the process above, but also creating an A or CNAME record for a purchase domain pointing to the server IP

Once this is done, you can run the following to automatically create a cert:

<pre class="wp-block-code"><code>sudo /usr/share/jitsi-meet/scripts/install-letsencrypt-cert.sh</code></pre>

## Reference

<https://github.com/jitsi/jitsi-meet/blob/master/doc/quick-install.md#advanced-configuration>

 [1]: https://jitsi.org/jitsi-meet/