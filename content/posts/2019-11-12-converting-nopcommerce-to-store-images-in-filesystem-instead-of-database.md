---
title: Converting nopCommerce to Store Images In Filesystem Instead of Database
author: dfar

date: 2019-11-12T19:27:25+00:00
url: /converting-nopcommerce-to-store-images-in-filesystem-instead-of-database/
categories:
  - Uncategorized

---
By default, nopCommerce stores all of the images in the database, making for simple access. However, with a lot of images, this puts a lot of load on the database, alongside making for a larger database to maintain.

<a rel="noreferrer noopener" aria-label="Some (opens in a new tab)" href="https://www.nopcommerce.com/boards/t/24487/pictures-in-database-or-file-system-what-performs-better.aspx" target="_blank">Some</a> <a rel="noreferrer noopener" aria-label="research (opens in a new tab)" href="https://www.nopcommerce.com/boards/t/45575/pictures-storage-database-or-disk.aspx" target="_blank">research</a> shows that the performance doesnt differ greatly between the two options, but here&#8217;s a few reasons you might use either:

### Storing in Database

  * Allows for easier backups & environment clones (only need to clone codebase and DB)
  * Single place for image storage (useful with multiple systems)

### Storing on Fileserver

  * Separates large portion of data from DB (allows for smaller DB)
  * Lowers load on DB server.

First, back up your database &#8211; since this is a process that can cause major issues if something were to go wrong (or if you decide to revert back to storing images in the database).

To convert from storing images in the database to the file server, go to Admin -> Settings -> Media Settings. Click on &#8216;Change&#8217;:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/11/image-3-1024x253.png" alt="" class="wp-image-740" srcset="https://40.76.37.251/wp-content/uploads/2019/11/image-3-1024x253.png 1024w, https://40.76.37.251/wp-content/uploads/2019/11/image-3-300x74.png 300w, https://40.76.37.251/wp-content/uploads/2019/11/image-3-768x190.png 768w, https://40.76.37.251/wp-content/uploads/2019/11/image-3.png 1619w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

After the conversion completes, nopCommerce will automatically convert all images into the /Content/Images folder. You&#8217;ll also notice the binaries no longer being stored in the `Picture` table.

## Shrink Database

Once this is done, you have the potential to open a good amount of space on the database, since the binaries of the images are now stored on the fileserver themselves.

To reclaim this space, you&#8217;ll need to shrink the database. Run the following command in SQL:

<pre class="wp-block-code"><code>DBCC SHRINKDATABASE (YOUR_DB, 10);
GO</code></pre>