---
title: Running Multi-Store NopCommerce Locally
author: dfar

date: 2020-11-10T17:31:33+00:00
url: /running-multi-store-nopcommerce-locally/
categories:
  - Technology

---
To run NopCommerce locally, you&#8217;ll need to set up a few things.

First, make an entry in your `hosts` file for each of the different stores, like so:

<pre class="wp-block-code"><code>127.0.0.1       store1.local
127.0.0.1       store2.local</code></pre>

Then inside your store settings, set the following for each store:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/11/image-1024x197.png" alt="" class="wp-image-1239" srcset="https://40.76.37.251/wp-content/uploads/2020/11/image-1024x197.png 1024w, https://40.76.37.251/wp-content/uploads/2020/11/image-300x58.png 300w, https://40.76.37.251/wp-content/uploads/2020/11/image-768x148.png 768w, https://40.76.37.251/wp-content/uploads/2020/11/image-1200x231.png 1200w, https://40.76.37.251/wp-content/uploads/2020/11/image.png 1292w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Finally, check that you can access the stores using the store URL provided.