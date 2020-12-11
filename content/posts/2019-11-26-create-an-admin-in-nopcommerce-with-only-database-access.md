---
title: Create An Admin in nopCommerce With Only Database Access
author: dfar

date: 2019-11-26T14:22:24+00:00
url: /create-an-admin-in-nopcommerce-with-only-database-access/
categories:
  - Technology

---
If you only have access to a database for nopCommerce (for example, if you&#8217;ve accidentally locked yourself out), here&#8217;s a way to create an administrator with access to the database and access to the frontend.

First, register a new user using the standard registration process. Once that&#8217;s done, run the following SQL query on the database to assign administrator rights:

<pre class="wp-block-code"><code>INSERT INTO Customer_CustomerRole_Mapping
VALUES (
	(SELECT Id FROM Customer
	WHERE Email = 'YOUR_EMAIL'),
	(SELECT Id FROM CustomerRole
	WHERE SystemName = 'Administrators')
)</code></pre>