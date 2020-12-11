---
title: Creating a Buy One Get One Half Off Discount In NopCommerce
author: dfar

date: 2019-06-19T17:04:58+00:00
url: /creating-a-buy-one-get-one-half-off-discount-in-nopcommerce/
categories:
  - Uncategorized

---
 

I recently worked with a client on trying to set up a Buy One Get One Half Off deal on NopCommerce &#8211; here&#8217;s what I did to get it working:

## Requirements

Before starting, you should have the following:

  * Have the [Has One Product Plugin][1] installed.
  * Have a product to apply the discount to.

## Process

Go to the Discounts page and create a new Discount with the following information:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-5.png" alt="" class="wp-image-445" /> </figure> 

Next, go to the &#8216;Restrictions&#8217; tab and add a restriction for the Product, using the &#8216;Add Product&#8217; functionality Make sure you add the `:2` to the end to force purchase of two:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-7.png" alt="" class="wp-image-447" /> </figure>

 [1]: https://www.nopcommerce.com/p/2980/has-one-product-discount-requirement-rule-nopcommerce-team.aspx