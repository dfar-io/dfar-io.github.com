---
title: Cleaning up Hard Drive Space in Linux
author: dfar

date: 2019-09-30T15:46:18+00:00
url: /cleaning-up-hard-drive-space-in-linux/
categories:
  - Uncategorized

---
If you run into a situation where a Linux machine is running out of space, here&#8217;s a way to check what is taking up so much space and clean the server up.

Install `ncdu`:

`sudo apt-get install ncdu`

Afterwards, start `ncdu` and you&#8217;ll be presented with an interface that&#8217;ll walk you through the directories taking the most space:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-13.png" alt="" class="wp-image-664" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-13.png 1011w, https://40.76.37.251/wp-content/uploads/2019/09/image-13-300x297.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-13-768x760.png 768w, https://40.76.37.251/wp-content/uploads/2019/09/image-13-100x100.png 100w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Troubleshooting &#8211; My Drive is 100% Full

If you&#8217;re having trouble installing the above, you likely have a full drive that cannot do anything else. Here are a few steps that may help:

First, see if there are any individual files you can delete to clear some space (such as in the user directory). If this isn&#8217;t an option, go into `/var/tmp` and delete the files in there () this is not always a safe choice).

After that, run the following:

<pre class="wp-block-code"><code>sudo apt-get clean
sudo apt install byobu
sudo purge-old-kernels
sudo apt autoremove
sudo update-grub</code></pre>

With this you should be able to install the above and check into what is taking up so much space.