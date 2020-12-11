---
title: Create Multiple Terraform Resources with a Map
author: dfar

date: 2019-06-07T00:26:34+00:00
url: /create-multiple-terraform-resources-with-a-map/
categories:
  - Uncategorized

---
I recently worked through a case in Terraform where I wanted to be able to pass in a map for Terraform to allow creating secrets in an Azure Key Vault. We&#8217;ll use the `count`, `keys(`, and `values(` helpers to iterate through the map.

This code sample will show a way to dynamically determine the amount of resources needed. In this case, this will create secrets based on the map provided in the Terraform variable:

<pre class="wp-block-code"><code>variable "vault_secrets" {
  secret_name_1 = "secret_value_1"
  secret_name_2 = "secret_value_2"
  secret_name_3 = "secret_value_3"
}

resource "azurerm_key_vault" "vault" {
  name                = "example-vault"
  location            = "${var.rg_location}"
  resource_group_name = "${var.rg_name}"
  tenant_id           = "00000000-0000-0000-0000-000000000000"

  sku {
    name = "standard"
  }
}

resource "azurerm_key_vault_secret" "secret" {
  count = "${length(keys(var.vault_secrets))}"

  name         = "${element(keys(var.vault_secrets), count.index)}"
  value        = "${element(values(var.vault_secrets), count.index)}"
  key_vault_id = "${azurerm_key_vault.vault.id}"
}</code></pre>