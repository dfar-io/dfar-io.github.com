---
title: Cleaning up Customer Data in nopCommerce
author: dfar

date: 2020-01-14T20:25:14+00:00
url: /cleaning-up-customer-data-in-nopcommerce/
categories:
  - Technology

---
 

Going through a nopCommerce project with a 40GB database, I found the majority of the space was allotted to a very large Customer table. To fix this, I decided to delete Customer data with the following criteria:

  * No username, email, and password data
  * Not a system account
  * Doesn&#8217;t reference a Shipping and Billing address
  * Last activity recorded a month from today&#8217;s date

I considered the customers having shopping cart items as well, but found that most of the junk data had one shopping cart item &#8211; I think this comes from a robot regularly adding specific items to a cart to check price.

Here&#8217;s the SQL used:

<pre class="wp-block-code"><code>DECLARE @customersToDelete TABLE (id int)

INSERT INTO @customersToDelete (id)
SELECT Id FROM Customer
WHERE
	Username IS NULL
	AND Email IS NULL
	AND Password is NULL
	AND IsSystemAccount = 0
	AND BillingAddress_Id IS NULL
	AND ShippingAddress_Id IS NULL
	AND LastActivityDateUtc &lt; DATEADD(week, -1, GETDATE())

DELETE FROM ShoppingCartItem WHERE CustomerId IN (SELECT * FROM @customersToDelete)

DELETE FROM GenericAttribute WHERE KeyGroup = 'Customer' AND EntityId IN (SELECT * FROM @customersToDelete)

DELETE FROM Customer_CustomerRole_Mapping WHERE Customer_Id IN (SELECT * FROM @customersToDelete)

DELETE FROM Customer WHERE Id IN (SELECT * FROM @customersToDelete)</code></pre>

Once this is done, make sure to shrink the database to reclaim the space gained by clearing out the data.