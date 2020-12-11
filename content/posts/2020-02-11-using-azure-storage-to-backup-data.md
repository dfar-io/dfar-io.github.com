---
title: Using Azure Storage to Backup Data
author: dfar

date: 2020-02-11T14:06:46+00:00
url: /using-azure-storage-to-backup-data/
categories:
  - Technology

---
If you&#8217;re looking for an easy and cheap way to back up infrequently used data, you can set up a storage account in Azure with the following settings:

  * Location: closest to your location.
  * Performance: Standard.
  * Account Kind: BlobStorage
  * Replication: Locally-redundant storage
  * Access tier: Cool

After the storage account is created, you can upload your compressed files (suggested to maximize efficiency) to containers inside the storage account.

Once the files are uploaded, you have the option to change to the **Archive** access tier, which is useful for files you don&#8217;t plan on accessing frequently. This data, if ever needed, will take much longer to access.

My suggestion on above &#8211; stick to the cool tier and when looking for ways to save money on costs, change different blobs to archive tier as you find you won&#8217;t need to access.

Check out <a rel="noreferrer noopener" aria-label="more details on pricing (opens in a new tab)" href="https://azure.microsoft.com/en-us/pricing/details/storage/blobs/" target="_blank">more details on pricing</a> and <a href="https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal" target="_blank" rel="noreferrer noopener" aria-label="more details on different access tiers (opens in a new tab)">more details on different access tiers</a>.