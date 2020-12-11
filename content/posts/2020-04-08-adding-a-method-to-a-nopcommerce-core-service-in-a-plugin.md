---
title: Adding a Custom Method to a nopCommerce Core Service in a Plugin
author: dfar

date: 2020-04-08T18:28:18+00:00
url: /adding-a-method-to-a-nopcommerce-core-service-in-a-plugin/
categories:
  - Technology

---
In nopCommerce, you can extend a core service to include a new method and new functionality using both dependency injection and inheritance. For this example, we&#8217;ll use TaxService provided in **Nop.Services**.

## Implementation

First, create a new interface in your plugin that inherits from the core service interface:

<pre class="wp-block-code"><code>// ICustomTaxService.cs

using Nop.Services.Tax;

namespace YourNamespace
{
    public interface ICustomTaxService : ITaxService
    {
        void YourCustomMethod(); 
    }
}</code></pre>

After creating the interface, create the implementation class, inheriting from both the newly created interface and the base service:

<pre class="wp-block-code"><code>// CustomTaxService.cs

namespace YourNamespace
{
    public class CustomTaxService : TaxService, ICustomTaxService
    {
        // constructor passing in all dependencies using base()
        public CustomTaxService(...) : base(...)
        
        public void YourCustomMethod() {
            // implementation here
        }
    }
}</code></pre>

Finally, make sure to connect the interface to the implementation in your plugin&#8217;s `DependencyRegistrar.cs` file:

<pre class="wp-block-code"><code>class DependencyRegistrar : IDependencyRegistrar
{
    public int Order { get { return 1; } }

    public void Register(ContainerBuilder builder, ITypeFinder typeFinder, NopConfig config)
    {
        builder.RegisterType&lt;CustomTaxService>().As&lt;ICustomTaxService>();
    }
}</code></pre>

## Usage

Once the above is created, you can reference the new service using the interface created (usually, this is done in a controller or a service):

<pre class="wp-block-code"><code>private readonly ICustomTaxService _customTaxService;

public WarrantyTaxService(
    ICustomTaxService customTaxService
){
    _customTaxService = customTaxService;
}

// now use the service for anything you need!</code></pre>