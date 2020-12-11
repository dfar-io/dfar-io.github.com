---
title: Applying IP Restrictions to a Large Set of Azure Resources
author: dfar

date: 2019-06-20T14:13:26+00:00
url: /applying-ip-restrictions-to-a-large-set-of-azure-resources/
categories:
  - Uncategorized

---
 

To do this, use PowerShell and Azure CLI to collect all of the NSGs and get all of the NSGs in the subscription:

<pre class="wp-block-code"><code>az account set -s &lt;SUB_ID>
$nsgs = az network nsg list | ConvertFrom-Json</code></pre>

Then go through every NSG and create the rule:

<pre class="wp-block-code"><code>$nsgs | ForEach-Object -Process { az network nsg rule create --name NAME --nsg-name $_.name --priority PRIORITY --resource-group $_.resourceGroup --protocol Tcp --destination-port-ranges 80 443 --source-address-prefixes IP_ADDRESSES }</code></pre>

Next, get a list of the App Services:

<pre class="wp-block-code"><code>$webapps = az webapp list | ConvertFrom-Json</code></pre>

And go through and add the list of IPs required (must use individual IPs):

<pre class="wp-block-code"><code>$webapps | ForEach-Object -Process { $WebAppConfig = Get-AzResource -ResourceName $_.name -ResourceType Microsoft.Web/sites/config -ResourceGroupName $_.resourceGroup -ApiVersion 2018-11-01; $WebAppConfig.Properties.ipSecurityRestrictions = @([PSCustomObject] @{ ipAddress = '127.0.0.1/32' },@{ ipAddress = '127.0.0.1/32' });  Set-AzResource -ResourceId $WebAppConfig.ResourceId -Properties $WebAppConfig.Properties -ApiVersion 2018-11-01 }</code></pre>

## Removing Access

To delete the same list of rules from the NSGs, use the same name:

<pre class="wp-block-code"><code>$nsgs | ForEach-Object -Process { az network nsg rule delete -g resourceGroup --nsg-name $_.name -n NAME }</code></pre>

Reference:

<https://docs.microsoft.com/en-us/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-list>

<https://docs.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create>

<https://docs.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-delete>