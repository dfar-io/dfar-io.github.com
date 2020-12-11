---
title: Setting up SFTP File Migration to Azure Storage Account
author: dfar

date: 2019-09-24T16:19:49+00:00
url: /setting-up-sftp-file-migration-to-azure-storage-account/
categories:
  - Uncategorized

---
 

This guide assumes you are using a Linux SFTP server for receiving files, and then uploading those files into an Azure storage account.

## Initial Setup

Install Azure CLI on the SFTP server with the following command:

 `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash` 

Then login using an account with access to the storage account:

`az login`

## Perform File Migration

The next step is actually sending the files over to a storage account. For this example, we&#8217;ll send all of the files in a particular folder to the storage account.

`az storage blob upload-batch -d YOUR_CONTAINER --account-name STORAGE_ACCOUNT_NAME -s DIRECTORY`

Once those files are moved over, move them into a `processed/` directory:

`mkdir /data/ftpuser/upload/processed`

`sudo mv * /processed/`

## Automate Migration Process

The final step is making sure to automate the process to allow for migrating files on a regular interval.

First, create a file named `/bin/migrate.sh`:

<pre class="wp-block-code"><code>#!/bin/bash

echo "Starting file -> AZ storage upload"
sudo mkdir -p /data/ftpuser/queued/
sudo mv /data/ftpuser/upload/* /data/ftpuser/queued/
az storage blob upload-batch -d CONTAINER_NAME --account-name STORAGE_ACCOUNT_NAME -s DIRECTORY
if [ $? -ne 0 ]; then
    echo FAIL
    exit 1;
fi
sudo mkdir -p /data/ftpuser/processed
sudo mv /data/ftpuser/queued/* /data/ftpuser/processed/</code></pre>

Allow the script to be executable:

`chmod u+x /bin/migrate.sh`

Then edit cronjobs to run every 15 minutes:

`sudo crontab -e`

<pre class="wp-block-code"><code>*/15 * * * * /bin/migrate.sh >> /var/log/migrate_output.log 2>&1</code></pre>

You can check the logs of the process here:

`/var/log/migrate_output.log`

## Reference

<https://kvz.io/schedule-tasks-on-linux-using-crontab.html>