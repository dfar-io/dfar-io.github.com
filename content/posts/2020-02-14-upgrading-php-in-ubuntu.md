---
title: Upgrading PHP in Ubuntu
author: dfar

date: 2020-02-14T17:51:38+00:00
url: /upgrading-php-in-ubuntu/
categories:
  - Technology

---
Before doing this, I recommend backing up your system, in case things go wrong.

To upgrade to the latest version of PHP in Ubuntu, first, update the system:

<pre class="wp-block-code"><code>sudo apt-get update && sudo apt-get upgrade</code></pre>

Then add the third-party repository:

<pre class="wp-block-code"><code>sudo apt-get install software-properties-common && sudo add-apt-repository ppa:ondrej/php && sudo apt-get update</code></pre>

Finally, upgrade PHP to <a rel="noreferrer noopener" aria-label="the latest version (opens in a new tab)" href="https://www.php.net/downloads.php" target="_blank">the latest version</a>:

<pre class="wp-block-code"><code>sudo apt-get install php7.4</code></pre>

If upgrading PHP for WordPress, you&#8217;ll need a few more modules:

<pre class="wp-block-code"><code>sudo apt-get install php7.4-mysql php7.4-curl php7.4-dom php7.4-imagick php7.4-mbstring php7.4-zip php7.4-gd</code></pre>

If using Apache, you&#8217;ll need to set the correct PHP version:

<pre class="wp-block-code"><code># list modules
sudo apache2ctl -M

# deactivate old PHP
sudo a2dismod php7.2

#activate new PHP
sudo a2enmod php7.4

#restart apache
sudo systemctl restart apache2</code></pre>