---
title: Setting up Remote State with Terraform and Azure
author: dfar

date: 2019-05-13T12:48:21+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-remote-state-with-terraform-and-azure/
categories:
  - Uncategorized

---
 <h2>Create the Azure Storage Account</h2> The first step is creating a storage account that will host the state information for the entire tenant. Create the following in order: <ol> <li>Resource group (specifically for the Storage Account)</li> <li>Storage Account</li> <li>A container/blob for the Storage Account</li> </ol> In terms of working with multiple projects, I personally like to use multiple storage accounts for each major project you&#8217;ll be building out. This gives a separate access key for each project. <h2>Setting up Backend Config</h2> First, create a file called <code>backend.tf</code> and populate with the following: <div> <pre>terraform { backend "azure" {} }</pre> </div> Second, create a file called <code>beconf.tfvars</code>, and populate it with the following: <div> resource\_group\_name = &#8220;&#8221; storage\_account\_name = &#8220;&#8221; container\_name = &#8220;&#8221; key = &#8220;&#8221; access\_key = &#8220;&#8221; Once this is done, initialize the backend with the following command: <code>terraform init -backend-config="beconf.tfvars"</code> Verify everything is set up with the following: <a href="http://40.76.37.251/wp-content/uploads/2019/05/2019-05-13-08\_44\_16-.png"><img class="alignnone wp-image-372" src="http://40.76.37.251/wp-content/uploads/2019/05/2019-05-13-08\_44\_16-.png" alt="" width="689" height="165" /></a> <h2>Running Terraform</h2> Once the backend configuration is set, the next step is running Terraform itself. I suggest running it alongside a <code>tfvars</code> file that defines environment-specific information: <code>terraform apply -var-file="dev.tfvars"</code> </div> <h2>Checking into Source Code</h2> Now that you&#8217;re using remote state for Terraform, you don&#8217;t have to worry about checking in your state file into source code. Just in case you&#8217;re doing this on a fresh Terraform setup, add the following to your <code>.gitignore</code> file: <pre>.terraform be.tfvars</pre> <pre></pre> <strong>Reference</strong> https://medium.com/@trstringer/use-azure-blob-storage-for-remote-terraform-state-5f809b95215a