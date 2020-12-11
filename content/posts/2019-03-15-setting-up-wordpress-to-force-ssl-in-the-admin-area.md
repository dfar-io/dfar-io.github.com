---
title: Setting up WordPress to force SSL in the Admin Area
author: dfar

date: 2019-03-15T15:51:17+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-wordpress-to-force-ssl-in-the-admin-area/
categories:
  - Uncategorized

---
 <p>Recently, I ran into an issue when trying to migrate an existing WordPress website to start using HTTPS, especially in the admin area. Even after migrating everything in the database over to use HTTPS, I ran into a 302 redirect issue when trying to log in.</p> <p><strong>NOTE: make sure an SSL certificate is configured for the site, or you&#8217;ll receive an invalid certificate error.</strong></p> <p>Add the following to your wp-config.php file at the very top <em>(if you don&#8217;t add to top, you&#8217;ll run into yet another issue where your users will not have permissions to access anything in admin)</em>:</p> <pre class="wp-block-preformatted"><?php<br /><br />define('FORCE\_SSL\_ADMIN', true);<br /><br />// in some setups HTTP\_X\_FORWARDED\_PROTO might contain <br />// a comma-separated list e.g. http,https<br />// so check for https existence<br />if (strpos($\_SERVER['HTTP\_X\_FORWARDED\_PROTO'], 'https') !== false)<br /> $\_SERVER['HTTPS']='on';<br /><br /><br />...</pre> <p>With this in place, you&#8217;ll see when you go into the administration area, you&#8217;ll always be redirected to use HTTPS.</p> <p>Reference:&nbsp;https://codex.wordpress.org/Administration\_Over\_SSL</p>