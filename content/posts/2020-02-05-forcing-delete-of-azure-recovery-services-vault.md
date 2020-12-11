---
title: Forcing Delete of Azure Recovery Services Vault
author: dfar

date: 2020-02-05T16:07:17+00:00
url: /forcing-delete-of-azure-recovery-services-vault/
categories:
  - Technology
  - Uncategorized

---
When trying to delete a Recovery Services vault, you can run into issues with trying to actually delete the vault from the tenant &#8211; to solve this issue, you&#8217;ll have to use the CLI and Azure CLI to do this.

Before starting, make sure to disable soft delete within the vault as well, and then delete any currently existing soft delete items.

<pre class="wp-block-code"><code>#login to tenant
az login

#get the item's container and item name
az backup item list --vault-name VAULT_NAME --resource-group RESOURCE_GROUP

#disable protection
az backup protection disable --container-name "CONTAINER_NAME" --delete-backup-data true --item-name "ITEM_NAME" --resource-group RESOURCE_GROUP --vault-name VAULT_NAME --yes --backup-management-type AzureIaasVM

#get list of policies
az backup policy list --vault-name VAULT_NAME --resource-group RG

#delete each policy
az backup policy delete --name POLICY_NAME --vault-name VAULT_NAME --resource-group RG

#delete vault
az backup vault delete --name VAULT_NAME --resource-group RG</code></pre>