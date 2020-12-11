---
title: Changing Default Token Expiration for Azure AD
author: dfar

date: 2019-10-31T17:56:44+00:00
url: /changing-default-token-expiration-for-azure-ad/
categories:
  - Uncategorized

---
To change the default token expiration timeframe when using Azure AD for authentication, you can do the following.

First, if you haven&#8217;t yet, install the `AzureADPreview` PowerShell Module:

<pre class="wp-block-code"><code>Install-Module AzureADPreview</code></pre>

Now, connect to Azure AD using an account that has access to manage App Registrations:

<pre class="wp-block-code"><code>Connect-AzureAD</code></pre>

After that, check and delete any policies that currently exist

After that, create a new policy (this one is set for 30 minutes as an example):

<pre class="wp-block-code"><code>$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"00:30:00","MaxAgeSessionSingleFactor":"00:30:00"}}') -DisplayName "CustomPolicy" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"</code></pre>

And apply that policy to the service principal tied to the Azure AD integration:

<pre class="wp-block-code"><code>$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '&lt;service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id</code></pre>

Now verify that the policy is in place:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-10-1024x126.png" alt="" class="wp-image-716" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-10-1024x126.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-10-300x37.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-10-768x94.png 768w, https://40.76.37.251/wp-content/uploads/2019/10/image-10.png 1509w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Reference

<https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-configurable-token-lifetimes>