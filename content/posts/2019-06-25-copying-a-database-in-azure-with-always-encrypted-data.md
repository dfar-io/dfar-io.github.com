---
title: Copying a Database in Azure with Always Encrypted Data
author: dfar

date: 2019-06-25T15:19:39+00:00
url: /copying-a-database-in-azure-with-always-encrypted-data/
categories:
  - Uncategorized

---
When trying to copy a database with Always Encrypted data (say, to a different environment), you&#8217;ll generally want to recycle the Column Master Key used to match the vault stored in the same Azure resource group. This takes a little bit of work to do:

## Pre-Reqs

You&#8217;ll need to have the following software installed:

  * SSMS
  * Azure CLI

You&#8217;ll also need to make sure the database you&#8217;re copying from has a key that already exists. Run the following query on your newly copied database:

<pre class="wp-block-code"><code>select * from sys.column_master_keys</code></pre>

And then check to see if the key exists in the appropriate vault:

<pre class="wp-block-code"><code>az keyvault key show --id KEY_PATH</code></pre>

If it exists, you&#8217;ll be able to copy the database over without issue.

## Procedure

Create a new key in the Key Vault:

<pre class="wp-block-code"><code>az keyvault key create --name Always-Encrypted-Auto1 --vault-name VAULT_NAME</code></pre>

Next, create a new Column Master Key, using the created key above.<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-9-1024x930.png" alt="" class="wp-image-558" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-9-1024x930.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-9-300x272.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-9-768x697.png 768w, https://40.76.37.251/wp-content/uploads/2019/07/image-9.png 1043w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

With two CMKs, rotate the initial CMK (using credentials from the source key vault, and then the destination key vault).<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/11/image.png" alt="" class="wp-image-733" srcset="https://40.76.37.251/wp-content/uploads/2019/11/image.png 519w, https://40.76.37.251/wp-content/uploads/2019/11/image-300x180.png 300w" sizes="(max-width: 519px) 85vw, 519px" /> </figure> <figure class="wp-block-image"><img src="https://dfar.io/wp-content/uploads/2019/11/image-1.png" alt="" class="wp-image-734" srcset="https://40.76.37.251/wp-content/uploads/2019/11/image-1.png 806w, https://40.76.37.251/wp-content/uploads/2019/11/image-1-300x78.png 300w, https://40.76.37.251/wp-content/uploads/2019/11/image-1-768x200.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /></figure> 

Next, clean up the previous CMK:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/11/image-2.png" alt="" class="wp-image-736" srcset="https://40.76.37.251/wp-content/uploads/2019/11/image-2.png 495w, https://40.76.37.251/wp-content/uploads/2019/11/image-2-300x189.png 300w" sizes="(max-width: 495px) 85vw, 495px" /> </figure> 



After this is done, you can delete the old CMK.