---
title: Restricting Access to App Services and Function Apps
author: dfar

date: 2020-06-19T13:20:13+00:00
url: /restricting-access-to-app-services-and-function-apps/
categories:
  - Technology

---
## Restrict By Function

Restriction by function works well since it gives you granular control over functions. An appropriate application would be giving the key to other applications, and then having those applications use the key to access.  
  
There are three levels to restrict a function:

  * Anonymous &#8211; no restrictions
  * Function &#8211; restricted by an individual function key
  * Admin &#8211; uses the admin key defined in the function app

When calling the function, there are two ways to do so, using either a query string:

<pre class="wp-block-code"><code>https:&#47;&#47;networking-test-fa.azurewebsites.net/api/HttpTrigger1?code=YOUR_CODE</code></pre>

Or using the `x-functions-key` header.

## Restrict by Application

### Restrict By IP

For Azure App Services and Function Apps, you can restrict access to them based on IP address &#8211; useful for building internal applications with limited access.

First, click **Networking** on the left sidebar, then open the **Access Restrictions** page.

From here, you can add and remove IP addresses for both the App service/Function App and the SCM page. If no IP addresses are listed, then access is open for all.

Any invalid IP addresses at this point will receive a 403:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/06/image-1024x306.png" alt="" class="wp-image-1208" srcset="https://40.76.37.251/wp-content/uploads/2020/06/image-1024x306.png 1024w, https://40.76.37.251/wp-content/uploads/2020/06/image-300x90.png 300w, https://40.76.37.251/wp-content/uploads/2020/06/image-768x229.png 768w, https://40.76.37.251/wp-content/uploads/2020/06/image.png 1178w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

If you need a list of the IPs trying to access the application, you can access:

  * App Service: Diagnose and solve problems -> Availabilty and Performance -> HTTP 4xx Errors
  * Function App: Diagnose and solve problems -> HTTP 4xx Errors -> HTTP 4xx Errors

Once here, scroll to the bottom of the page and expand **Which client IPs got rejected due to IP restriction?**:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/06/image-1-1024x402.png" alt="" class="wp-image-1212" srcset="https://40.76.37.251/wp-content/uploads/2020/06/image-1-1024x402.png 1024w, https://40.76.37.251/wp-content/uploads/2020/06/image-1-300x118.png 300w, https://40.76.37.251/wp-content/uploads/2020/06/image-1-768x302.png 768w, https://40.76.37.251/wp-content/uploads/2020/06/image-1-1536x603.png 1536w, https://40.76.37.251/wp-content/uploads/2020/06/image-1-2048x804.png 2048w, https://40.76.37.251/wp-content/uploads/2020/06/image-1-1200x471.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

### App Service Authentication

App Service Authentication works well when working with an application that is accessed by a browser (so not an API). Note that without authentication, you&#8217;ll need to login before accessing the app at all.

First, turn on App Service Authentication:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/07/image-801x1024.png" alt="" class="wp-image-1220" srcset="https://40.76.37.251/wp-content/uploads/2020/07/image-801x1024.png 801w, https://40.76.37.251/wp-content/uploads/2020/07/image-235x300.png 235w, https://40.76.37.251/wp-content/uploads/2020/07/image-768x982.png 768w, https://40.76.37.251/wp-content/uploads/2020/07/image.png 1088w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> </figure> 

For setting up the source, setting up Azure Active Directory allows for using users inside the tenant for access. Useful for setting up an internal application.