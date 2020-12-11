---
title: Automatically Upgrading Azure Kubernetes Services
author: dfar

date: 2019-09-25T18:28:16+00:00
url: /automatically-upgrading-azure-kubernetes-services/
categories:
  - Uncategorized

---
Azure provides a means to upgrade Kubernetes clusters using the AKS service. This guide will walk you through using an automation account to upgrade the services on a regular basis, making the process something you don&#8217;t need to worry about.

Note that you may want to hold off on doing this for production systems &#8211; if for some reason an upgrade were to break currently functionality, there is no means for reverting a cluster back to an original version.

## Create a Powershell Core Function App

First, create a function app that runs on PowerShell Core:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-10.png" alt="" class="wp-image-649" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-10.png 464w, https://40.76.37.251/wp-content/uploads/2019/09/image-10-137x300.png 137w" sizes="(max-width: 464px) 85vw, 464px" /> </figure> 

After creating the function app, you&#8217;ll also want to make sure to increase the standard timeout rate, since this call can take some time to process. Change **host.json** to have the following:

<pre class="wp-block-code"><code>{
    "functionTimeout": "00:10:00"
}</code></pre>

If you have a large number of clusters you&#8217;ll be checking regularly, you should use a standard App Service plan instead, to remove the timeout entirely.

## Import Azure CLI into the Function App

Next, you&#8217;ll want to import Azure CLI into the Function App, to allow for calling the `az` command.

First, you&#8217;ll need to install Azure CLI on you local machine. You&#8217;ll be copying this directory created into the function app created to use, so after installing, locate the Azure CLI files at **C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2**.

Connect to FTP using the publish profile for the Function App (access this through the portal) and copy the entire **CLI2** folder into the /home directory. __**Make sure that all of the files are copied successfully.**

To verify everything is working, run the following command:

 `D:/home/CLI2/wbin/az.cmd`

If you get a successful call back, you&#8217;ve imported Azure CLI correctly and can now write the code to programmatically upgrade the AKS clusters.

## Create Service Principal

Next, you&#8217;ll create a service principal that has access to the clusters in mind, so you have an account that can log in and perform the upgrade duties. Run the following command locally when logged in to the subscription desired:

<pre class="wp-block-code"><code>az ad sp create-for-rbac -n "aks-autoupgrade-sp" --role contributor --scopes /subscriptions/{SubID}</code></pre>

After this is done, you should receive output showing the name, password, and tenant. Add the three of these as configuration values for the Function App as:

  * AZ_USER &#8211; appId
  * AZ_PASS &#8211; password
  * AZ_TENANT &#8211; tenant

## Create Timer Function

Next, create a timer function that runs every day, let&#8217;s say at noon:

`0 0 12 * * *`

Use the following codebase.

<pre class="wp-block-code"><code>param($Timer)

Write-Output "Logging in as Service Principal $env:AZ_USER"
D:/home/CLI2/wbin/az.cmd login --service-principal -u $env:AZ_USER -p $env:AZ_PASS --tenant $env:AZ_TENANT | Out-Null

if (!($?)) {
    Write-Error "Login failed, exiting script."
    exit 1;
}

Write-Output "Getting list of AKS clusters...";
$aksClusters = D:/home/CLI2/wbin/az.cmd aks list | ConvertFrom-Json;

if ($aksClusters.length -le 0) {
  Write-Output "No AKS clusters found, exiting...";
  exit 1;
}

$aksClusters | ForEach-Object -Process {
  $clusterName = $_.name
  Write-Output "$clusterName : checking for upgrades..."
  $upgrades = D:/home/CLI2/wbin/az.cmd aks get-upgrades `
    -g $_.resourceGroup -n $_.name | ConvertFrom-Json
  $agentPoolProfiles = $upgrades.agentPoolProfiles

  if ($agentPoolProfiles.upgrades -eq $null) {
    Write-Output "No upgrades available."
    return;
  }

  $latestUpgrade = $agentPoolProfiles.upgrades[-1];

  $currentKubernetesVersion = $upgrades.agentPoolProfiles.kubernetesVersion;
  $newKubernetesVersion = $latestUpgrade.kubernetesVersion

  Write-Output `
    "Upgrade available: $currentKubernetesVersion => $newKubernetesVersion"

  Write-Output "Upgrading to $newKubernetesversion automatically."
  D:/home/CLI2/wbin/az.cmd aks upgrade -k $newKubernetesVersion -g $_.resourceGroup -n $_.name --no-wait --yes
}

Write-Output "Complete."
exit 0;</code></pre>

You can run the function to make sure it is running as intended, while commenting out the `az aks upgrade` line to ensure no upgrades occur.

## Setting up Failure Alerts

The final (optional) step is setting up a means to alert in case of failure. When creating the Function App, an Application Insights resource should have been created as well. Go the &#8216;Alerts&#8217; section in the App Insight resource, and create an alert:



Add your email as an action group to notify if there is an issue with failures.

## Reference

<https://stackoverflow.com/questions/56544059/azure-cli-commands-not-working-inside-azure-function-apps-portal>