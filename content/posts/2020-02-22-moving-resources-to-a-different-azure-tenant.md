---
title: Moving Resources to a Different Azure Tenant
author: dfar

date: 2020-02-22T19:11:10+00:00
url: /moving-resources-to-a-different-azure-tenant/
categories:
  - Technology

---
Let&#8217;s say you have some resources in your Azure tenant that you want to move to a different tenant (such as if you&#8217;re going to transfer ownership of a particular set of resources to another company). You can do that with the following steps:

## Move Existing Resources to A New Subscription

  1. Create a new subscription in your current tenant as the current account admin (use your own information, which you will change over later so you can be an Account Admin).
  2. Rename the subscription to something more meaningful to avoid confusion.
  3. Sign into your tenant and move the desired resources over to the new subscription (you may need to recreate certain resources as some resources cannot be moved).
      1. If running any VM backups, you&#8217;ll need to delete all images before you can move a VM over. After moving over, make sure to turn backups back on.

## Transfer Subscription

Next, create a new Microsoft account, and create an Azure account to transfer the subscription to &#8211; using the phone and credit card of the responsible party. When going through this process, you&#8217;ll either need the person by you when setting up the account, or have them set it up on their own.

Once the account is created, go back to the newly created subscription from above and transfer billing ownership using the email account of the Azure account from above:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/02/image-5-1024x825.png" alt="" class="wp-image-1068" srcset="https://40.76.37.251/wp-content/uploads/2020/02/image-5-1024x825.png 1024w, https://40.76.37.251/wp-content/uploads/2020/02/image-5-300x242.png 300w, https://40.76.37.251/wp-content/uploads/2020/02/image-5-768x619.png 768w, https://40.76.37.251/wp-content/uploads/2020/02/image-5.png 1055w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Setting up the transfer request will send an email to the above &#8211; and give them the directions to accept the transfer.

## Additional Steps

Once the transfer is done, they will own the resources. Here&#8217;s a few more steps you may want to take:

### Set up Custom Domain for Azure AD

To set up the ability to use a domain for login to the tenant, you&#8217;ll need DNS access to a preferred domain.

### Create an Administrative User

If you want to keep the ability to administer the resources, create a user for yourself and give them the following:

  * Global Admin rights in Azure AD
  * Owner rights to the subscription