---
title: Adding Settings to a Plugin in NopCommerce (pre-4.00)
author: dfar

date: 2020-04-30T13:00:34+00:00
url: /adding-settings-to-a-plugin-in-nopcommerce-pre-4-00/
categories:
  - Technology

---
Once you&#8217;re <a rel="noreferrer noopener" href="https://dfar.io/creating-a-nopcommerce-plugin-in-version-3-90-and-below/" target="_blank">created a plugin for NopCommerce</a>, you&#8217;ll likely want to add the ability to configure settings inside the plugin for reference later.

When adding this capability, we&#8217;re going to work on trying to make this as immutable as possible, to follow functional programming as best we can, just because it makes things a little cleaner and puts all of the conversion between the configuration model and settings object.

### Locale Keys

First, we&#8217;ll set up a class called `MyPluginLocaleKeys.cs` that holds the different locale keys used to represent the settings descriptions in the Configure page:

<pre class="wp-block-code"><code>public static class MyPluginLocaleKeys
{
    public const string MySetting1 = "Plugins.Misc.MyPlugin.Fields.Settings1";
    public const string MySetting2 = "Plugins.Misc.MyPlugin.Fields.Settings2";
}</code></pre>

### Configuration Model

Next, we&#8217;ll create a configuration model named `Models/MyPluginConfigModel.cs` that represents the configure page for the plugin. This will get populated by the plugin&#8217;s settings, and then be responsible for receiving user data and saving the setting values provided.

In addition to usually having a 1:1 relationship to the plugin settings, you can also provide DataAnnotations that handle model validity and display names. When providing a `NopResourceDisplayName`, we&#8217;ll use the locale key provided above.

<pre class="wp-block-code"><code>public class MyPluginConfigModel
{
    &#91;Required]
    &#91;NopResourceDisplayName(MyPluginLocaleKeys.Setting1)]
    public string Setting1 { get; set; }

    &#91;NopResourceDisplayName(MyPluginLocaleKeys.Setting2)]
    public string Setting2 { get; set; }
}</code></pre>

### Settings Object

Next, create an ISettings implementation in the root of the plugin named `MyPluginSettings.cs`.

This is going to work a little differently &#8211; we&#8217;re going to create this to be immutable, so we&#8217;ll both initialize it from a config model, create a &#8220;default&#8221; settings object, and allow for easily converting to a config model.

<pre class="wp-block-code"><code>public class MyPluginSettings : ISettings
{
    // Settings properties
    public string Setting1 { get; private set; }
    public string Setting2 { get; private set; }

    // Initializes from a config model (used for saving values)
    public static MyPluginSettings FromModel(MyPluginConfigModel)
    {
        return new MyPluginSettings()
        {
            Setting1 = model.Setting1,
            Setting2 = model.Setting2
        };
    }

    // Create a "default" settings object that is used when installing plugin
    public static MyPluginSettings Default()
    {
        return new MyPluginSettings()
        {
            Setting1 = "Default setting",
            Setting2 = "Another default setting"
        };
    }

    // Creates a config model to use for the configuration page
    public MyPluginConfigModel ToModel()
    {
        return new MyPluginConfigModel()
        {
            Setting1 = Setting1,
            Setting2 = Setting2
        }
    }
}</code></pre>

We use `private set` here to make sure we initialize the settings object from a configuration model (when saving) and disallow the ability to change the settings object &#8211; making it immutable.

### Base Plugin Class

Next, create the base plugin class (named `MyPluginPlugin.cs`, so for example, `EnhancedLoggingPlugin.cs`, used for defining the type of plugin and defining the install/uninstall options. We&#8217;ll do a few special things 

<pre class="wp-block-code"><code>public class MyPluginPlugin : BasePlugin, YOUR_PLUGIN_INTERFACE
{
    private readonly ISettingService _settingService;

    public MyPluginPlugin(
        ISettingService settingService)
    {
        _settingService = settingService;
    }

    public override void Install()
    {
        // saves the desired default values for settings
        _settingService.SaveSetting(MyPluginSettings.Default());
        
        // Adds all locale resources
        foreach (var field in typeof(MyPluginLocaleKeys).GetFields())
	{
	    string key = (string)field.GetValue(null);
	    // Converts the key above to a value (capital letters separated by space)
	    string value = Regex.Replace(field.Name, "(&#91;a-z])(&#91;A-Z])", "$1 $2");
	    value = Regex.Replace(value, "(&#91;A-Z])(&#91;A-Z]&#91;a-z])", "$1 $2");

	    this.AddOrUpdatePluginLocaleResource(key, value);
	}

        base.Install();
    }

    public override void Uninstall()
    {
        // Delete the plugin settings from DB
        _settingService.DeleteSetting&lt;MyPluginSettings>();

        // Delete all plugin locales using reflection
        foreach (var field in typeof(MyPluginLocaleKeys).GetFields())
	{
	    this.DeletePluginLocaleResource((string)field.GetValue(null));
	}

        base.Uninstall();
    }
}</code></pre>

### Configure Controller and View

The final step is creating both the Configuration controller and view to finish the ability to view and save plugin settings data. Create the `Controllers/MyPluginConfigController.cs` file:

<pre class="wp-block-code"><code>&#91;AdminAuthorize]
public class SliExportController : BasePluginController
{
    private readonly MyPluginSettings _settings;
    private readonly ISettingService _settingService;
    private readonly ILocalizationService _localizationService;

    public SliExportController(
        SliExportSettings sliExportSettings,
	ISettingService settingService,
	ILocalizationService localizationService)
    {
	_sliExportSettings = sliExportSettings;
	_settingService = settingService;
	_localizationService = localizationService;
    }

    &#91;ChildActionOnly]
    public ActionResult Configure()
    {
        return View("~/Plugins/PLUGIN_EXPORT/Views/Configure.cshtml", _settings.ToModel());
    }

    &#91;HttpPost]
    &#91;ChildActionOnly]
    public ActionResult Configure(MyPluginConfigModel model)
    {
        if (!ModelState.IsValid)
	{
	    return Configure();
	}

	_settingService.SaveSetting(SliExportSettings.FromModel(model));
        SuccessNotification(
            _localizationService.GetResource("Admin.Plugins.Saved"));

        return Configure();
    }
</code></pre>

Then create the `Views/Configure.cshtml` file:

<pre class="wp-block-code"><code>@{
    Layout = "";
}
@model YOUR_CONFIG_MODEL
@using Nop.Web.Framework;
@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    &lt;div class="panel-group">
        &lt;div class="panel panel-default">
            &lt;div class="panel-body">
                &lt;div class="form-group">
                    &lt;div class="col-md-3">
                        @Html.NopLabelFor(model => model.Setting1)
                    &lt;/div>
                    &lt;div class="col-md-9">
                        @Html.NopEditorFor(model => model.Setting1)
                        @Html.ValidationMessageFor(model => model.Setting1)
                    &lt;/div>
                &lt;/div>
                &lt;div class="form-group">
                    &lt;div class="col-md-3">
                        @Html.NopLabelFor(model => model.Setting2)
                    &lt;/div>
                    &lt;div class="col-md-9">
                        @Html.NopEditorFor(model => model.Setting2)
                        @Html.ValidationMessageFor(model => model.Setting2)
                    &lt;/div>
                &lt;/div>
                &lt;div class="form-group">
                    &lt;div class="col-md-3">
                        &nbsp;
                    &lt;/div>
                    &lt;div class="col-md-9">
                        &lt;input type="submit" name="save" class="btn bg-blue" value="@T("Admin.Common.Save")" />
                    &lt;/div>
                &lt;/div>
            &lt;/div>
        &lt;/div>
    &lt;/div>
}
</code></pre>