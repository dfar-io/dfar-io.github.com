---
title: Delete Recovery Containers in Azure
author: dfar

date: 2019-02-25T14:38:22+00:00
excerpt: |
  
  <![CDATA[]]>
url: /delete-recovery-containers-in-azure/
categories:
  - Uncategorized

---
 <p>While doing some clean-up of a collection of Azure resources, I came across difficulty when trying to delete <a href="https://azure.microsoft.com/en-us/services/site-recovery/">Azure Site Recovery</a> resources. Specifically, when trying to delete the Recovery Services vault, I&#8217;d receive this &#8216;Vault deletion error&#8217;:</p> <figure class="wp-block-image"><img src="http://40.76.37.251/wp-content/uploads/2019/02/image-5.png" alt="Vault deletion error message" class="wp-image-277"/></figure> <p>Furthermore, when trying to use the portal to delete the existing containers, I would end up with a delay in the actual deletion of the containers. I ended up looking through Azure CLI does and finding a way to programmatically delete the containers.</p> <p>First, find the subscription ID of your Azure tenant, and use the following command:</p> <pre class="wp-block-preformatted">Connect-AzureRmAccount -Subscription "<SUBSCRIPTION\_ID>"</pre> <p>Next, connect to the Azure Recovery Vault using the Resource Group name and Vault name:</p> <pre class="wp-block-preformatted">$rv = Get-AzureRmRecoveryServicesVault -ResourceGroupName "<RESOURCE\_GROUP\_NAME>" -Name "<VAULT\_NAME>"<br />Set-AzureRmRecoveryServicesVaultContext -Vault $rv<br /></pre> <p>Finally, you can run the following command to delete a recovery container from the vault. Use this for each of the containers:</p> <pre class="wp-block-preformatted">$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -FriendlyName "<CONTAINER\_NAME>" -ResourceGroupName "<RESOURCE\_GROUP_NAME>"<br /><br />$BackupItem = Get-AzureRmRecoveryServicesBackupItem -Container $Cont -WorkloadType "AzureVM"<br /><br />Disable-AzureRmRecoveryServicesBackupProtection -Item $BackupItem -RemoveRecoveryPoints -Force</pre> <p>After deleting all the containers, you should be able to delete the Recovery vault without any issues.</p>