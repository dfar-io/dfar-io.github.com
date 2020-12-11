---
title: 'Fixing “unable to get credential storage lock: File exists” when Deploying Function App via Jenkins'
author: dfar

date: 2019-07-24T14:39:16+00:00
url: /fixing-unable-to-get-credential-storage-lock-file-exists-when-deploying-function-app-via-jenkins/
categories:
  - Uncategorized

---
When working with deploying Azure Function Apps with Jenkins, I ran into an issue when trying to rebuild a Function App from scratch with the same name. I was unable to deploy the codebase via Pipeline due to the following error:

<pre class="wp-block-code"><code>unable to get credential storage lock: File exists</code></pre>

I was able to fix by doing the following:

SSH into the server.

Log in as the user that runs when running a Jenkins job (for example):

<pre class="wp-block-code"><code>sudo su -s /bin/bash jenkins</code></pre>

Access the user&#8217;s `.git-credentials` file, and remove the reference to the pre-existing Function App SCM.

Delete the `.git-credentials.lock` file.

After doing this, try running the job again and ensure the issue has been solved.