---
title: Test Disk I/O Benchmarks in Linux
author: dfar

date: 2020-02-07T22:16:45+00:00
url: /test-disk-i-o-benchmarks-in-linux/
categories:
  - Technology
  - Uncategorized

---
To test read/write speeds of a drive in Linux, you can run `dd` to check write speed:

<pre class="wp-block-code"><code>dd if=/dev/zero of=./test bs=512k count=2048 oflag=direct</code></pre>

and read speed (need to clear cache beforehand to get an accurate reading, <a href="https://www.unixtutorial.org/test-disk-speed-with-dd" target="_blank" rel="noreferrer noopener" aria-label="read more (opens in a new tab)">read more</a>):

<pre class="wp-block-code"><code>sudo /sbin/sysctl -w vm.drop_caches=3
dd if=./test of=/dev/zero bs=512k count=2048</code></pre>

Should get a result like the following for write speed:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/02/image-1-1024x148.png" alt="" class="wp-image-895" srcset="https://40.76.37.251/wp-content/uploads/2020/02/image-1-1024x148.png 1024w, https://40.76.37.251/wp-content/uploads/2020/02/image-1-300x43.png 300w, https://40.76.37.251/wp-content/uploads/2020/02/image-1-768x111.png 768w, https://40.76.37.251/wp-content/uploads/2020/02/image-1-1200x173.png 1200w, https://40.76.37.251/wp-content/uploads/2020/02/image-1.png 1523w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

and read speed:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/02/image-2-1024x233.png" alt="" class="wp-image-896" srcset="https://40.76.37.251/wp-content/uploads/2020/02/image-2-1024x233.png 1024w, https://40.76.37.251/wp-content/uploads/2020/02/image-2-300x68.png 300w, https://40.76.37.251/wp-content/uploads/2020/02/image-2-768x174.png 768w, https://40.76.37.251/wp-content/uploads/2020/02/image-2-1200x272.png 1200w, https://40.76.37.251/wp-content/uploads/2020/02/image-2.png 1242w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure>