---
title: Upgrading Jenkins On Azure
author: dfar

date: 2018-08-11T18:51:37+00:00
excerpt: |
  
  <![CDATA[]]>
url: /upgrading-jenkins-on-azure/
categories:
  - Uncategorized

---
<!--[CDATA[On July 18 2018, it was announced that Jenkins had a major security flaw in the system, prompting an immediate fix and deploy.  When I set up the Jenkins instance for Azure, I used the Azure Marketplace to do so, handling most of the legwork in getting everything set up.  A quick Google search hadn't brought up an easy way to do this - so I took a good half of a day to figure out the process of upgrading Jenkins on Azure.
This guide will help you upgrade Jenkins on Azure, assuming you've used the <a href="https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins" target="_blank" rel="noopener noreferrer"-->Jenkins Azure Marketplace to create the Jenkins instance.&nbsp; This assumes you&#8217;ve already created a Jenkins virtual machine on Azure.&nbsp; In addition, the initial set up should be complete.&nbsp; &nbsp;If not, you can check out some more information within 

<a href="https://docs.microsoft.com/en-us/azure/jenkins/" target="_blank" rel="noopener noreferrer">the Microsoft documentation</a>.

## SSH into the Virtual Machine

If you&#8217;ve been using this instance, you should be familiar with SSH forwarding and being able to access Jenkins using localhost.&nbsp; If this is new, you can read <a href="https://jenkins.io/blog/2017/04/20/secure-jenkins-on-azure/" target="_blank" rel="noopener noreferrer">this Jenkins article</a> on the security setup for the Azure Jenkins Marketplace instance.  
Use this command in a command line interface to log in to your virtual machine:

<pre class="lang:default decode:true">ssh -L 127.0.0.1:8080:localhost:8080 &lt;username&gt;@&lt;domain_name&gt;</pre>

Replace the following:

  * Username &#8211; the server admin username created when creating the Azure VM.
  * Domain Name &#8211; the domain name to access the virtual machine.

You&#8217;ll be asked for a password &#8211; use the password provided alongside the username when creating the virtual machine.  
After logging in you&#8217;ll see a screen like below.&nbsp; This verifies you are connected to the virtual machine.  
[<img class="alignnone wp-image-123 size-full" src="http://40.76.37.251/wp-content/uploads/2018/08/Capture.png" alt="logged into the virtual machine" width="745" height="376" />][1]  
Access **http://localhost:8080** and verify you can access the Jenkins interface:  
[<img class="alignnone wp-image-124 size-large" src="http://40.76.37.251/wp-content/uploads/2018/08/Capture-1-1024x600.png" alt="Jenkins interface via localhost" width="750" height="439" />][2]

## Download Newest JAR

After logging into the server, you&#8217;ll use the following command to download the latest version of Jenkins:

<pre class="lang:default decode:true">sudo wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war</pre>

Assuming all is successful, you should see the following  
<img class="alignnone wp-image-89 size-large" src="http://40.76.37.251/wp-content/uploads/2018/07/Capture-1024x354.png" alt="Downloaded newest Jenkins JAR" width="750" height="259" />  
After downloading the .war file, copy it to the Jenkins directory:

<pre class="lang:default decode:true">sudo mv jenkins.war /usr/share/jenkins/jenkins.war</pre>

_(if you need to know the location of the WAR file on the server, you can check it in Manage Jenkins -> System Information)_  
And finally, with the new .war in place, restart Jenkins:

<pre class="lang:default decode:true">sudo systemctl restart jenkins</pre>

Verification of the installation is simple. Access Jenkins and check the bottom right to see the updated version number:  
[<img class="alignnone wp-image-125 size-full" src="http://40.76.37.251/wp-content/uploads/2018/08/Untitled.png" alt="Checked for latest Jenkins installation" width="574" height="56" />][3]

## Reference

[Upgrade Jenkins server to a new&nbsp;version][4]  
<https://wiki.jenkins.io/display/JENKINS/Automated+Upgrade>]]>

 [1]: http://40.76.37.251/wp-content/uploads/2018/08/Capture.png
 [2]: http://40.76.37.251/wp-content/uploads/2018/08/Capture-1.png
 [3]: http://40.76.37.251/wp-content/uploads/2018/08/Untitled.png
 [4]: https://mohitgoyal.co/2017/02/15/upgrade-jenkins-server-to-a-new-version/