---
title: Setting up Jenkins to Auto-Update
author: dfar

date: 2019-09-11T20:53:45+00:00
url: /setting-up-jenkins-to-auto-update/
categories:
  - Uncategorized

---
When setting up Jenkins, you&#8217;ll have to perform upgrades on the system yourself. <a rel="noreferrer noopener" aria-label="You can do this pretty easily (opens in a new tab)" href="https://dfar.io/upgrading-jenkins-on-azure/" target="_blank">You can do this pretty easily</a>, but let&#8217;s automate the process so that each night, Jenkins will download and install the latest version.

To do this you&#8217;ll need to have SSH access to the server.

## Creating Upgrade Script

First, SSH into the server.

Create a `bin/` folder in the user&#8217;s directory:

<pre class="wp-block-code"><code>cd ~
mkdir bin</code></pre>

If it doesn&#8217;t already exist, create a `.bash_profile` file and add the following:

<pre class="wp-block-code"><code> export PATH=$PATH:/home/&lt;YOUR_USER>/bin</code></pre>

Now create the script file for use:

<pre class="wp-block-code"><code>cd bin
touch upgrade-jenkins</code></pre>

Create a script with the following contents:

<pre class="wp-block-code"><code>#!/bin/bash

sudo wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war
sudo mv jenkins.war /usr/share/jenkins/jenkins.war
sudo systemctl restart jenkins</code></pre>

And make the script executable:

<pre class="wp-block-code"><code>chmod u+x upgrade-jenkins</code></pre>

You can finally test the script by calling `./upgrade-jenkins`. When it runs, Jenkins should restart with the latest version.

## Setting up for Automatic Updates

The next step is setting up the script to run each night at some point to promote automatic upgrades, so you don&#8217;t have to worry about updating the system. To do this, we&#8217;ll create a cron job that runs the above script once a day.

Set up a crontab file using `sudo crontab -e`, and add the following to the file to run everyday at 3am. (make sure to check `date` to see if the system is using UTC):

<pre class="wp-block-code"><code>0 3 * * * /home/YOUR_USER/bin/upgrade-jenkins >> /var/log/upgrade_jenkins.log 2>&1</code></pre>

Once this is in place, you&#8217;ll be running the script above once a day, which will download and run the latest version of Jenkins on a regular basis.

If you&#8217;re running into issues, you can check the logs at:

<pre class="wp-block-code"><code>/var/log/upgrade_jenkins.log</code></pre>

  
Reference:

<https://www.taniarascia.com/how-to-create-and-use-bash-scripts/>