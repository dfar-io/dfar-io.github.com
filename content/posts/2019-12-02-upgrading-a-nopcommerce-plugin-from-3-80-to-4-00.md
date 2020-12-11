---
title: Upgrading a NopCommerce Plugin from 3.80 To 4.00
author: dfar

date: 2019-12-02T16:11:22+00:00
url: /upgrading-a-nopcommerce-plugin-from-3-80-to-4-00/
categories:
  - Technology

---
When upgrading nopCommerce, you&#8217;ll need to also get the latest versions of plugins to go alongside the nopCommerce installation. Depending on the upgrade, you may need to make some minor changes to the plugin to get it working.

In particular, 3.80 -> 4.00 marks the transition to .NET Core, meaning there are some significant changes to make.

## Upgrade the .csproj File

To start, you&#8217;ll want to upgrade the `.csproj` file of the project. Easiest option here is to copy an existing plugin that works with nopCommerce 4.00 and modify it accordingly. You can use the `Description.txt` file to get the information you need.

## File Cleanup

First, create a `plugin.json` file.

Delete the following files:

  * web.config
  * app.config
  * Description.txt
  * Properties folder

## Fix Build

Once you&#8217;ve done this, you&#8217;ll need to go through the file and make any kind of modifications to get a build to work successfully. If you&#8217;re working with VSCode, add the following file `omnisharp.json` to the plugin:

<pre class="wp-block-code"><code>{
  "RoslynExtensionsOptions": {
    "enableAnalyzersSupport": true
  },
  "MsBuild": {
    "UseLegacySdkResolver": true
  }
}</code></pre>

There are a few specifics to consider:

  * When migrating `GetConfigurationRoute`, the route will be moved into a `Infrastructure/RouteProvider.cs` file (as an <a rel="noreferrer noopener" aria-label="example (opens in a new tab)" href="https://github.com/nopSolutions/PayPal-SmartButtons-plugin-for-nopcommerce/blob/nopCommerce-4.20/Nop.Plugin.Payments.PayPalSmartPaymentButtons/Infrastructure/RouteProvider.cs" target="_blank">example</a>).
  * `BasePublicController` becomes `BasePluginController`.
  * `AdminAuthorize` becomes `AuthorizeAdmin`.
  * Remove references to `ChildActionOnly`.

## Validate Functionality

After getting the build to work successfully, the final step is running nopCommerce locally and installing/uninstalling the plugin. In addition, check the functionality to make sure everything is working.

Reminder: when installing/uninstalling plugins in nopCommerce locally, make sure to shut down and restart the application after installing applications to reflect plugin installation changes.