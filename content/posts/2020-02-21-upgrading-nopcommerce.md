---
title: Upgrading nopCommerce
author: dfar

date: 2020-02-21T14:37:19+00:00
url: /upgrading-nopcommerce/
categories:
  - Technology

---
Upgrading nopCommerce is a manual process that requires a decent amonut to work to get in place. When upgrading, you&#8217;ll need to upgrade sequentially (3.80 -> 3.90 -> 4.00 -> etc.).

**NOTE**: When performing this process on a live site, **make sure to close the store during the upgrade process** so no changes occur on the site during upgrade.

## Upgrade Database

First, you&#8217;ll need to upgrade the database using a script provided by nopCommerce. [Download the script here][1] based on the version and apply the changes in the SQL script to a copied version of the database.

## Upgrade Site Files

The script above with have a README.txt file that describes the next steps, which is essentially:

  * Download the source code for the upgrade version
  * Porting over the specific configuration files (Settings.txt, InstalledPlugins.txt, etc.) and **making sure to point to the newly created database above**.

If storing images on the filesystem, make sure to copy images over in /Content/images as well.

At this point, you should be able to see the front page of the site.

## Restore Theme

If you were using a theme in the previous version, you&#8217;ll need to get the correct version of it and restore it.

First, if there are any SQL scripts for the theme, run those on the upgraded database.

Upload the theme to the nopCommerce installation, alongside any plugins required by the theme.

After this is done, check the site again and confirm the new theme is being used.

## Restore Plugins

Finally, go through the plugins from the original site and add the appropriate version. If you&#8217;re using the source code version of nopCommerce, you&#8217;ll want to make sure the plugin is set up to use the latest version of nopCommerce, and that it compiles.

Some plugins will require database upgrades as well.

 [1]: https://github.com/nopSolutions/nopCommerce/tree/develop/upgradescripts