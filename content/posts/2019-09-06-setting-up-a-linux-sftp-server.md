---
title: Setting up a Linux SFTP Server
author: dfar

date: 2019-09-06T18:17:48+00:00
url: /setting-up-a-linux-sftp-server/
categories:
  - Uncategorized

---
To get started, you&#8217;ll need to use the following:

  * A virtual machine with a public IP address.
  * SSH address to the above virtual machine

## Configuring the User

First, SSH into the server and install OpenSSH:

`sudo apt install -y ssh openssh-server`

Now create the group to have access, alongside a user to be able to access:

<pre class="wp-block-code"><code>sudo groupadd sftpg
sudo useradd -g sftpg ftpuser</code></pre>

Now set up the directories to allow for the following:

  * The user can view the contents of their own folder.
  * The user can upload files to the `uploads/` folder.

<pre class="wp-block-code"><code>sudo mkdir -p /data/ftpuser/upload
sudo chown -R root.sftpg /data/ftpuser
sudo chown -R ftpuser.sftpg /data/ftpuser/upload</code></pre>

## Setting up User Access

There are two ways to have the user access &#8211; by key or by password. Key is preferred in production scenarios as it&#8217;s overall more secure.

### By Key

Basically, you will be storing the public key on the FTP server, and the client connecting into the server will use the associated private key. Easiest way is to have the client generate a public/private key pair and then add the public key to `/home/ftpuser/.ssh/authorized_keys`.

Once that&#8217;s done, make sure the `ftpuser` user has ownership of the `authorized_keys` file.

### By Password

To allow for password, just use the following command:

<pre class="wp-block-code"><code>ssudo passwd ftpuser</code></pre>

## Setting up SFTP

To set up SFTP, append the following to the end of `/etc/ssh/sshd_config`:

<pre class="wp-block-code"><code>Match Group sftpg
     ChrootDirectory /data/%u
     ForceCommand internal-sftp</code></pre>

As you&#8217;re using a password to authenticate the above user, make sure the following is in the file as well:

`PasswordAuthentication yes`

Restart the service:

`<code>sudo systemctl restart sshd`</code>

## Verification

To verify everything is in place, try logging into the server as the new user:

`<code>sftp ftpuser@YOUR_DOMAIN`</code>

Once connected in, try uploading a file:

`PUT D:/local-file.png file.png`

And then check that the file is there:

`GET file.png D:/downloaded-file.png`