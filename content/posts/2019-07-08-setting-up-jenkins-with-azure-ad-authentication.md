---
title: Setting up Jenkins with Azure AD Authentication
author: dfar

date: 2019-07-08T17:52:48+00:00
url: /setting-up-jenkins-with-azure-ad-authentication/
categories:
  - Uncategorized

---
### Configuring Azure AD {#CreatingaJenkinsserverthroughAzure-ConfiguringAzureAD}

Run the following command in CLI to generate a service principal:

<table class="wp-block-table">
  <tr>
    <td>
      <code>az ad sp create-</code><code>for</code><code>-rbac --name=</code><code>"{NAME}"</code>&nbsp;<code>--role=</code><code>"Contributor"</code>&nbsp;<code>--scope=</code><code>"/subscriptions/{SUBSCRIPTION_ID}"</code>&nbsp;<code>--years=</code><code>100</code>
    </td>
  </tr>
</table>

Save the output generated, as you&#8217;ll use it for configuration in Jenkins.

Create a Redirect URL to [https://YOURHOST/securityRealm/finishLogin][1]<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-10-1024x152.png" alt="" class="wp-image-562" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-10-1024x152.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-10-300x45.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-10-768x114.png 768w, https://40.76.37.251/wp-content/uploads/2019/07/image-10.png 1382w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Set Required Permissions in Azure Active Directory to:

  * Application Permissions (Read Directory Data)
  * Delegated Permissions (Read Directory Data)

Click on &#8216;Grant permissions&#8217;.<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-11-1024x472.png" alt="" class="wp-image-564" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-11-1024x472.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-11-300x138.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-11-768x354.png 768w, https://40.76.37.251/wp-content/uploads/2019/07/image-11.png 1033w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

If planning to use an Azure AD group for authorization, create one now.

### Configuring Jenkins {#CreatingaJenkinsserverthroughAzure-ConfiguringJenkins}

Download the &#8216;Azure AD&#8217; plugin, and restart after installation.

Go to Manage Jenkins&nbsp;→ Configure Global Security.

Select &#8216;Enable Security&#8217; if it isn&#8217;t already selected.

Under &#8216;Security Realm&#8217;, select &#8216;Azure Active Directory&#8217;, and fill the information:

  * Client ID &#8211; &#8216;appId&#8217;
  * Client Secret &#8211; &#8216;password&#8217;
  * Tenant &#8211; &#8216;tenant&#8217;

Use the button to verify the application.

Set Authorization to &#8216;Azure Active Directory Matrix-based security&#8217;.

Set the Group to be the newly created, and assign the appropriate permissions.

Verify by logging out and logging back in as Azure AD user.

## Troubleshooting {#CreatingaJenkinsserverthroughAzure-Troubleshooting}

If you accidentally lock yourself out after enabling Azure AD, do the following:

SSH into the server.

modify the config.xml file

<table class="wp-block-table">
  <tr>
    <td>
      <code>sudo nano /var/lib/jenkins/config.xml</code>
    </td>
  </tr>
</table>

  * For the useSecurity item, change to &#8216;false&#8217;
  * Remove authorizationStrategy and securityRealm sections.

Restart Jenkins:

<table class="wp-block-table">
  <tr>
    <td>
      <code>sudo systemctl restart jenkins</code>
    </td>
  </tr>
</table>

Jenkins is now completely unprotected &#8211; so continue working on whatever security strategy you were working on.

 [1]: http://bams-jenkins.eastus.cloudapp.azure.com/securityRealm/finishLogin