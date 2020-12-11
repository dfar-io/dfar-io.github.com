---
title: Creating a nopCommerce Plugin in version 3.90 and below
author: dfar

date: 2019-03-27T01:18:01+00:00
excerpt: |
  
  <![CDATA[]]>
url: /creating-a-nopcommerce-plugin-in-version-3-90-and-below/
categories:
  - Uncategorized

---
<!--[CDATA[
</p-->

I recently worked with a client using an older version of nopCommerce that needed to have a plugin made for functionality. In particular, this plugin integrates with updown.io to allow for turning checks on and off during scheduled maintenance.

To do this, you&#8217;ll need to have the nopCommerce source code available for use.

## Creating the Plugin

To get started, create a Class Library in the Plugins folder:

  * Make sure to change the location to the plugin folder below the name.
  * Use naming convention Nop.Plugin.{category}.{name}

  * The categories you can use include:
      * ExternalAuth
      * Widgets
      * ExchangeRate
      * <g class="gr_ gr\_72 gr-alert gr\_spell gr\_inline\_cards gr\_run\_anim ContextualSpelling ins-del multiReplace" id="72" data-gr-id="72">DiscountRules</g>
      * Payments
      * Shipping
      * Tax
      * Misc

Next, go into properties and make sure your Target framework is .NET Framework 4,.5.1.

Next, change the output path for all configurations to deploy to:

<pre class="wp-block-preformatted">..\..\Presentation\Nop.Web\Plugins\{group}.{name}\<br /></pre><figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2019/03/image-16.png" alt="" class="wp-image-333" /> <figcaption> </figcaption></figure> 

This will build the output of the plugin into the Web project, which will ultimately deploy to the nopCommerce application.

After that, copy a web.config file from another plugin.

Next, create a `Description.txt` file with the following content:

<pre class="wp-block-preformatted">Group: {group}<br />FriendlyName: {friendly name}<br />SystemName: {group}.{name}<br />Version: 0.1<br />SupportedVersions: {your version of nopCommerce}<br />Author: {you}<br />DisplayOrder: 1<br />FileName: Nop.Plugin.{group}.{name}.dll<br />Description: {a description}<br /></pre>

Set both the web.config and Description.txt files to &#8216;Copy if newer&#8217;.

Finally, there is a Class.cs file that was created when creating the Class Library. Rename the file to {name}Plugin.cs and use the following code snippet:

<pre class="wp-block-preformatted">public class YourPlugin : BasePlugin, IMiscPlugin
    {
        public void GetConfigurationRoute(out string actionName, out string controllerName, out RouteValueDictionary routeValues)
         {
             actionName = "";
             controllerName = "";
             routeValues = new RouteValueDictionary { { "area", null } };
         }
    }</pre>

When building, you&#8217;ll be warned to add a few references as a result of referencing the Nop.Core project. Add the following NuGet packages:

  * Microsoft.Bcl.Build
  * Microsoft.Bcl
  * Microsoft.Bcl.Async
  * Microsoft.Net.Http

Once this is done, you should be able to clean, build, and publish the project. Check the plugins list of the admin backend to see your plugin listed:<figure class="wp-block-image">

<img src="http://40.76.37.251/wp-content/uploads/2019/03/image-18.png" alt="" class="wp-image-335" /> </figure> 

Just to ensure everything is working, go ahead and install the plugin. The plugin should install successfully, and the plugin will then be running on your site. You&#8217;re now ready to make changes to the plugin to modify NopCommerce capabilities.

## Next Steps

After the initial plugin was created, next steps include:

  * Creating a logo named logo.png and upload it to the plugin. Set the image to &#8216;Copy if newer&#8217; and size the image to around 50&#215;50.
  * [Set up a configuration page][1] to allow for customizing data on the plugin.

Reference:&nbsp;  
<http://docs.nopcommerce.com/pages/viewpage.action?pageId=22675509>

 [1]: https://dfar.io/setting-up-a-configuration-page-for-your-3-90-or-below-nopcommerce-plugin/