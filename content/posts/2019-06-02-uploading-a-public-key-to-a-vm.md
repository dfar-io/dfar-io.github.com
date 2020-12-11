---
title: Uploading a Public Key to a VM
author: dfar

date: 2019-06-02T15:48:39+00:00
excerpt: |
  
  <![CDATA[]]>
url: /uploading-a-public-key-to-a-vm/
categories:
  - Uncategorized

---
This will allow for This guide assumes you are trying to access a VM with a different machine than the one used to originally create the VM. <h2>Obtaining Public Key</h2> First, check to see if you already have a public key for your machine: <code>ls ~/.ssh</code> If you don&#8217;t see an <code>id\_rsa.pub</code> file, create a public/private key pair on the machine that will access the VM: <code>ssh-keygen</code> <h2>Uploading</h2> For the next step, you&#8217;ll need to use a machine that can access the VM &#8211; using the public key generated in the step above, SSH into the VM and run the following: <code>scp /path/to/public\_key username@server:~</code> <code>mv id\_rsa.pub ~/.ssh/authorized\_keys</code> <h2>Verification</h2> Finally, going back to the machine you want to SSH with, SSH into the VM to make sure access is working: <code>ssh username@hostname</code> You should be able to log into without a password.