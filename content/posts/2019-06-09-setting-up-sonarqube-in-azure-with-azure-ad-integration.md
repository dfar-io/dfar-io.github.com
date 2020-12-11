---
title: Setting Up SonarQube in Azure with Azure AD Integration
author: dfar

date: 2019-06-09T03:27:00+00:00
url: /setting-up-sonarqube-in-azure-with-azure-ad-integration/
categories:
  - Uncategorized

---
## Installing SonarQube {#SettingUpSonarQubeinAzurewithAzureADIntegration-InstallingSonarQube}

Go to the marketplace and install SonarQube from Bitnami &#8211;&nbsp;<https://bitnami.com/stack/sonarqube/cloud/azure>&nbsp;with the following:

  * resize to D1_v2
  * if desired, configure any NSG restrictions

After creation, set a DNS name label for the IP address.

Access using the domain name, and follow the directions in the link above to log in.

## Setting up Azure AD Integration {#SettingUpSonarQubeinAzurewithAzureADIntegration-SettingupAzureADIntegration}

### Setting up HTTPS {#SettingUpSonarQubeinAzurewithAzureADIntegration-SettingupHTTPS}

To be able to use Azure AD, you have to use HTTPS.

Set up the Server base URL to be the configured HTTPS address in Administration → Configuration → General → Server base URL:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image.png" alt="" class="wp-image-417" /> </figure> 

SSH into the server and set up a Let&#8217;s Encrypt script and enforce redirection to HTTPS here (make sure you aren&#8217;t behind a firewall to allow for Let&#8217;s Encrypt).

  * <https://docs.bitnami.com/aws/how-to/generate-install-lets-encrypt-ssl/>
  * <https://docs.bitnami.com/bch/apps/wordpress/administration/force-https-apache/>

Restart Apache: 

<pre class="wp-block-code"><code>sudo /opt/bitnami/ctlscript.sh restart apache</code></pre>

Verify that trying to access the HTTP version of SonarQube automatically redirects you to the HTTPS version.

### Creating App Registration in Azure {#SettingUpSonarQubeinAzurewithAzureADIntegration-CreatingAppRegistrationinAzure}

Run the following command in CLI to generate a service principal:

<pre class="wp-block-code"><code>az ad sp create-for-rbac --name="{NAME}" --role="Contributor" --scope="/subscriptions/{SUBSCRIPTION_ID}" --years=100</code></pre>

You should receive some output data, which you will use when filling out info in SonarQube:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-2.png" alt="" class="wp-image-419" /> </figure> 

Set the Reply URL of the application to https://<URL>/oauth2/callback/aad: <figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-1.png" alt="" class="wp-image-418" /> </figure> 

Set the required permissions:

  * Sign in and read user profile
  * Read all users&#8217; basic profiles<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-3.png" alt="" class="wp-image-420" /> </figure> 

### Installing Plug-in {#SettingUpSonarQubeinAzurewithAzureADIntegration-InstallingPlug-in}

Install the Azure Active Directory (AAD) Authentication Plug-in for SonarQube (restarting when requested):<figure class="wp-block-image">

![][1] </figure> 

Configure in Administration&nbsp;→ General Settings → Azure Active Directory:

  1. Activate Azure AD
  2. Use the value in &#8220;appId&#8221;
  3. Use the value in &#8220;password&#8221;
  4. Use the value in &#8220;tenant&#8221;
  5. Allow users to sign-up.

Log out as admin, and verify the ability to log in as an Azure AD user.

 [1]: https://bamsmcg.atlassian.net/wiki/download/thumbnails/577962004/image2019-4-8_12-21-4.png?version=1&modificationDate=1554740466177&cacheVersion=1&api=v2&width=1213&height=250