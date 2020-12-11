---
title: Creating an Angular and Azure Function API App with Azure Static Web App
author: dfar

date: 2020-05-27T01:12:28+00:00
url: /creating-an-angular-and-azure-function-api-app-with-azure-static-web-app/
categories:
  - Technology

---
Recently, Azure released Azure Static Web Apps, which looks like a way to host static web sites easily. Some of the perks I see immediately are:

  * Works well with SPA technologies (Angular, React, Vue)
  * Serve an API using Azure Functions
  * Automatic integration with GitHub and GitHub Actions to deploy immediately
  * Currently costs nothing (while this is in preview)

## Pre-requisites

To get started, you&#8217;ll need:

  * Angular CLI
  * Azure Functions Core Tools
  * An Azure account

## Create a GitHub Repo with Angular and Azure Function Apps

First, create a repo in your GitHub account, and clone that repo to your local PC.

Now create an Angular app with the CLI:

<pre class="wp-block-code"><code>ng new NAME --directory app</code></pre>

Next, create an Azure Functions API **(currently, there is a limitation that [only allows for use of Javascript][1] as the runtime)**:

<pre class="wp-block-code"><code>func init NAME --javascript
mv NAME api
cd api
func new --name TestFunction --language dotnet --template HttpTrigger</code></pre>

Commit the changes made above, and then let&#8217;s move onto creating the Azure Static Web App.

## Creating Azure Static Web App

Next, create an Azure Static Web App in your Azure account. When doing this, do the following:

  * Sign in to your GitHub account and select the correct repository and branch.
  * For build details, use the following information (replacing _azure-static-web-app-poc_ with):
      * App location: **app**
      * Api location: **api**
      * App artifact location: **dist/APPNAME**

## Automatic Deployment

After creating the Static Web App, a GitHub Workflows file will be created and committed to your repo. In turn, your skeleton application should be built and deployed automatically.

With the deployment completed, you can view the deployed UI and API by checking the URL of the Static Web App in the Azure portal:

  * UI &#8211; check the URL provided.
  * API &#8211; check the URL, plus `/api/TestFunction`<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/05/image-1-1024x180.png" alt="" class="wp-image-1184" srcset="https://40.76.37.251/wp-content/uploads/2020/05/image-1-1024x180.png 1024w, https://40.76.37.251/wp-content/uploads/2020/05/image-1-300x53.png 300w, https://40.76.37.251/wp-content/uploads/2020/05/image-1-768x135.png 768w, https://40.76.37.251/wp-content/uploads/2020/05/image-1-1536x270.png 1536w, https://40.76.37.251/wp-content/uploads/2020/05/image-1-2048x360.png 2048w, https://40.76.37.251/wp-content/uploads/2020/05/image-1-1200x211.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Setting a Custom Domain

To set up a custom domain, access the &#8216;Custom Domains&#8217; option on the sidebar. You&#8217;ll create a CNAME record as requested for the domain being used.

In terms of SSL Certificates, you don&#8217;t have to worry. Azure Static Web Apps will handle this completely for you. However, I was only able to create a www domain (as opposed to a root domain).

## Further Reading

Microsoft&#8217;s Guide on Static Web Apps: <a href="https://docs.microsoft.com/en-us/azure/static-web-apps/overview" target="_blank" rel="noreferrer noopener">https://docs.microsoft.com/en-us/azure/static-web-apps/overview</a>

 [1]: https://docs.microsoft.com/en-us/azure/static-web-apps/apis#constraints