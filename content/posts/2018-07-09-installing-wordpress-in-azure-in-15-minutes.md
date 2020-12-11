---
title: Installing WordPress in Azure In 15 Minutes
author: dfar

date: 2018-07-09T12:00:33+00:00
excerpt: |
  
  <![CDATA[]]>
url: /installing-wordpress-in-azure-in-15-minutes/
featured_image: /wp-content/uploads/2018/07/azure-wordpress.png
categories:
  - Uncategorized

---
<!--[CDATA[For a client last week, I've been working on creating a WordPress instance in Microsoft Azure.  This is due to the hosting setup they have, and a desire to have all things hosted on the cloud.
Generally, a set-up like this can be overkill for a simple WordPress page.  However, this makes the most sense if you're working on a WordPress instance that you expect to get a large amount of traffic, alongside having the ability to scale resources on the fly.
If you're looking to install a WordPress site for a personal blog or other low-traffic site, this isn't the most optimal way, since you're generally running ~$34 a month on the cheapest available settings, for the following:

<ul-->

  * ~$9 a month for a custom domain that does not supports using SSL 
      * If you&#8217;re interested in having SSL support, you&#8217;ll need to jump to the B1 level.&nbsp; This costs ~$32 a month.
  * ~$25 a month for the lowest setting capabilities for Azure Database for MySQL
This assumes a fresh installation of WordPress, with no existing databases available on your current Azure instance.&nbsp; If you have a MySQL Server already set up in Azure, you&#8217;ll change the directions a bit to just use the existing instance.

## Step 1: Create a WordPress Resource

After logging into the Azure portal, click on &#8216;Create a resource.&#8217;&nbsp; Search for &#8216;WordPress and select the image published by WordPress.  
You&#8217;ll be asked to provide some information, including:

  * **App Name**: a unique name you would like to use for the app.
  * **Resource Group**: type in the name of the new resource group to include the resources in.&nbsp; You can also add these resources to an existing resource group.
  * **App Service plan/Location**: select the app service plan to use for the WordPress instance.&nbsp; this is what drives the overall cost for all resources running &#8211; you can read more <a href="https://azure.microsoft.com/en-us/pricing/details/app-service/windows/" target="_blank" rel="noopener noreferrer">here</a>.
  * **Database**:&nbsp; To use WordPress, Azure needs to create a MySQL server.&nbsp; Azure provides using the &#8216;Azure Database for MySQL&#8217; service.&nbsp; Using this, you&#8217;ll set the server admin credentials and the pricing tier to be used.&nbsp; This will also create a database name for you for immediate use that you can change to use a more friendly name.

After about 5 minutes, the WordPress instance should be created.&nbsp; You can test it by accessing the URL **https://{your-app-name}.azurewebsites.net.&nbsp;&nbsp;**If you can see the WordPress installation screen, you&#8217;re good to go.  
&nbsp;

## Step 2: Complete WordPress Installation

Once the database user has been created, run through the standard WordPress installation.&nbsp; If you&#8217;re familiar with this process, you&#8217;ll notice the wp-config screen is skipped.&nbsp; That&#8217; because the file is already generated for you when installing the system in Azure.  
Although this is the fastest way to get started, there are a few issues that you&#8217;ll want to address as you begin development:

  * Setting up a database user that only has access to the newly created database.&nbsp; This ensures that the user accessing the database only have the rights needed for WordPress.&nbsp; If the wp-config.php file is compromised, you don&#8217;t want to have the server admin credentials exposed.
  * Securing the Azure Database for MySQL Server to only allow access for particular IP addresses.&nbsp; The current setup allows for all IP addresses to connect in.&nbsp; Convenient, but not very secure.
  * Adding an SSL connection requirement.&nbsp; This ensures a secure connection to the database server.

I&#8217;ll write more on the above in the upcoming weeks on securing your WordPress installation.]]>