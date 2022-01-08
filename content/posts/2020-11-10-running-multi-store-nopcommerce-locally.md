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

```
127.0.0.1       store1.local
127.0.0.1       store2.local
```

Then inside your store settings, set the following for each store: https://dfar.io/wp-content/uploads/2020/11/image-1024x197.png

Finally, check that you can access the stores using the store URL provided.
