---
title: Set up Auto-Renewing Let’s Encrypt Certificates on App Service Linux
author: dfar

date: 2020-03-09T13:23:15+00:00
url: /set-up-auto-renewing-lets-encrypt-certificates-on-app-service-linux/
categories:
  - Technology

---
Set up the ability to assign auto-renewing Let&#8217;s Encrypt certs to an Azure App Service Linux instance with shibayan&#8217;s <a rel="noreferrer noopener" aria-label="appservice-acmebot (opens in a new tab)" href="https://github.com/shibayan/appservice-acmebot" target="_blank">appservice-acmebot</a>. This will create a Function App that automatically updates the App Service as needed.

Before getting started, you&#8217;ll need to have a few things:

  * An Azure App Service (Linux-based).
  * DNS records being served using Azure DNS.

## Procedure

First, <a rel="noreferrer noopener" aria-label="deploy the solution (opens in a new tab)" href="https://github.com/shibayan/appservice-acmebot#1-deploy-to-azure-functions" target="_blank">deploy the solution</a> to your Azure subscription. Use the following for configation:

  * Resource group &#8211; certbot-rg
  * App Name Prefix &#8211; certbot-fa
  * Mail Address &#8211; _your email address._

Next, set up the ability for the Certbot function app to access your Linux App Service<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/03/image-1-1024x320.png" alt="" class="wp-image-1096" srcset="https://40.76.37.251/wp-content/uploads/2020/03/image-1-1024x320.png 1024w, https://40.76.37.251/wp-content/uploads/2020/03/image-1-300x94.png 300w, https://40.76.37.251/wp-content/uploads/2020/03/image-1-768x240.png 768w, https://40.76.37.251/wp-content/uploads/2020/03/image-1-1536x479.png 1536w, https://40.76.37.251/wp-content/uploads/2020/03/image-1-2048x639.png 2048w, https://40.76.37.251/wp-content/uploads/2020/03/image-1-1200x375.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

  * Select &#8216;App Service Authentication&#8217; to on.
  * Set &#8216;Log in with Azure Active Directory&#8217;
  * Set up the &#8216;Azure Active Directory&#8217; Auth Provider
      * Set Management mode to &#8216;Express&#8217;
      * Create a new AD app
  * Save changes.<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/03/image-2-595x1024.png" alt="" class="wp-image-1097" srcset="https://40.76.37.251/wp-content/uploads/2020/03/image-2-595x1024.png 595w, https://40.76.37.251/wp-content/uploads/2020/03/image-2-174x300.png 174w, https://40.76.37.251/wp-content/uploads/2020/03/image-2-768x1322.png 768w, https://40.76.37.251/wp-content/uploads/2020/03/image-2-892x1536.png 892w, https://40.76.37.251/wp-content/uploads/2020/03/image-2.png 980w" sizes="(max-width: 595px) 85vw, 595px" /> </figure> 

Now, assign the **Website Contributor** and **Web Plan Contributor** roles to the Certbot function app:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/03/image-462x1024.png" alt="" class="wp-image-1094" srcset="https://40.76.37.251/wp-content/uploads/2020/03/image-462x1024.png 462w, https://40.76.37.251/wp-content/uploads/2020/03/image-135x300.png 135w, https://40.76.37.251/wp-content/uploads/2020/03/image.png 605w" sizes="(max-width: 462px) 85vw, 462px" /> </figure> 

Finally, **restart the function app** and access the Function App URL (https://_your-function-app_.azurewebsites.net/add-certificate). You should be able to see the UI showing the available RGs and App Names available (access can take 30-60 minutes to reflect in Azure, so if you&#8217;re seeing a 401, wait a while for things to resolve):<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/03/image-3-1024x828.png" alt="" class="wp-image-1101" srcset="https://40.76.37.251/wp-content/uploads/2020/03/image-3-1024x828.png 1024w, https://40.76.37.251/wp-content/uploads/2020/03/image-3-300x243.png 300w, https://40.76.37.251/wp-content/uploads/2020/03/image-3-768x621.png 768w, https://40.76.37.251/wp-content/uploads/2020/03/image-3.png 1129w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Once the certificate is added, you&#8217;re all set! Renewals will automatically occur, as the app will scan regularly to check when to renew the certs.