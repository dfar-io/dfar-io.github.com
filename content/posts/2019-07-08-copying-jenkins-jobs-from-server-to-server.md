---
title: Copying Jenkins Jobs from Server to Server
author: dfar

date: 2019-07-08T18:23:33+00:00
url: /copying-jenkins-jobs-from-server-to-server/
categories:
  - Uncategorized

---
In the case where you want to copy a collection of jobs from one Jenkins server to another, here&#8217;s a process you can use to make the migration. This guide assumes you have two different remote Jenkins instances to copy jobs from.

SSH into the source server to determine where the `/jobs` directory is for Jenkins. Example would be in /

Use `scp` (**on your local machine**) to copy the jobs from the remote Jenkins instance with the jobs:

<pre class="wp-block-code"><code>scp -r USER@DNS:~JENKINS/jobs C:\Users\d\Downloads\jobs</code></pre>

After everything is downloaded, upload the files to the new server (you will need to upload them to the home directory of the user, and then move them via SSH):

<pre class="wp-block-code"><code>scp -r C:\Users\d\Downloads\jobs DEST_USER@DEST_DNS:~</code></pre>

After that completes, SSH into the server again and move the files:

<pre class="wp-block-code"><code>sudo mv jobs /var/lib/jenkins</code></pre>

Also, set the permissions of the jobs file over to the Jenkins service user:

<pre class="wp-block-code"><code>sudo chown -R jenkins:jenkins /var/lib/jenkins/jobs</code></pre>

After this is complete, go into Settings and Reload Configuration from Disk. Ensure that all of your previous jobs are in place.