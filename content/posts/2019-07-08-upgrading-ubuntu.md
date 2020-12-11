---
title: Upgrading Ubuntu
author: dfar

date: 2019-07-08T15:26:25+00:00
url: /upgrading-ubuntu/
categories:
  - Uncategorized

---
To upgrade Ubuntu, use the following procedure:

Upgrade all of your current dependencies:

<pre class="wp-block-code"><code>sudo apt update && sudo apt upgrade</code></pre>

Change over dependencies with new versions of packages:

<pre class="wp-block-code"><code>sudo apt dist-upgrade && sudo apt-get autoremove</code></pre>

Perform the OS upgrade:

<pre class="wp-block-code"><code>sudo apt install update-manager-core && sudo do-release-upgrade</code></pre>