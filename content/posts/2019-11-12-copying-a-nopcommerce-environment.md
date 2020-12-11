---
title: Copying a nopCommerce Environment
author: dfar

date: 2019-11-12T20:57:03+00:00
url: /copying-a-nopcommerce-environment/
categories:
  - Uncategorized

---
Copying a nopCommerce environment to a destination such as your local machine or to another environment requires two major steps:

  * Copying the database
  * Cloning the source code and configuring to use the copied database

## Copy The Database

  1. Using SSMS, log in to the source database server using administrative credentials (usually the remote environment).
  2. On source database server, right-click on database and click Tasks -> Export Data-tier Application
  3. Save the .bacpac file to the destination machine.
  4. Connect to the destination database server (usually local).
  5. Right-click &#8216;Databases&#8217; on the destination server and click &#8216;Import Data-tier Application&#8217;.
  6. Use the .bacpac file created from the source database.

## Copy and Configure the Source Code

  1. Clone the source code to the destination machine.
  2. Create the /Presentation/Nop.Web/App_Data/dataSettings.json file with the following content:

<pre class="wp-block-code"><code>{
  "DataProvider": "sqlserver",
  "DataConnectionString": "Data Source=&lt;YOUR_DB_SERVER_NAME>;Initial Catalog=&lt;YOUR_DB_NAME>;Integrated Security=True;",
  "RawDataSettings": {}
}
</code></pre>

Example:

<pre class="wp-block-code"><code>{
  "DataProvider": "sqlserver",
  "DataConnectionString": "Data Source=localhost\\SQLEXPRESS;Initial Catalog=uesaka;Integrated Security=True;",
  "RawDataSettings": {}
}</code></pre>

Next, verify the site can be accessed.

Finally, make sure to change the store URL to the new URL being used.

## Troubleshooting with Nop-templates

If you&#8217;re using nop-templates, you&#8217;ll run into some issues when trying to copy.

I had to delete all contents from the following SQL table:

`delete from SS_C_Condition`

Of course, this currently disables all HTML Widgets &#8211; so there must be a better way to do this. Take a look at <https://www.nopcommerce.com/boards/t/50457/adding-or-editing-quick-tab-item-with-same-key-already-added.aspx>