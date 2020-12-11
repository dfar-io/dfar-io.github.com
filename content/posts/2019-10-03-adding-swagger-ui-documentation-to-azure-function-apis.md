---
title: Adding Swagger UI Documentation to Azure Function APIs
author: dfar

date: 2019-10-03T01:13:39+00:00
url: /adding-swagger-ui-documentation-to-azure-function-apis/
categories:
  - Uncategorized

---
You can set up Swagger UI in your Azure Function API to allow for providing documentation for your serverless API pretty easily.

## Initial Configuration

First, add the SwashBuckle library to your process via the <project>.csproj file:

<pre class="wp-block-code"><code>&lt;ItemGroup>
    &lt;PackageReference ... />
    &lt;PackageReference Include="AzureFunctions.Extensions.Swashbuckle" Version="1.4.1" />
    ...
&lt;/ItemGroup></code></pre>

Next set up the SwashBuckle startup code in `SwashBuckleStartup.cs`:

<pre class="wp-block-code"><code>using System.Reflection;
using AzureFunctions.Extensions.Swashbuckle;
using YOUR_NAMESPACE;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Hosting;

[assembly: WebJobsStartup(typeof(SwashBuckleStartup))]
namespace YOUR_NAMESPACE
{
  internal class SwashBuckleStartup : IWebJobsStartup
  {
    public void Configure(IWebJobsBuilder builder)
    {
      builder.AddSwashBuckle(Assembly.GetExecutingAssembly());
    }
  }
}</code></pre>

Now create both HTTP Triggers for the Swagger document:

<pre class="wp-block-code"><code>[FunctionName("SwaggerJson")]
[SwaggerIgnore]
public static Task&lt;HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "json")]
        HttpRequestMessage req,
    ILogger log,
    [SwashBuckleClient] ISwashBuckleClient swashBuckleClient)
{
  return Task.FromResult(swashBuckleClient.CreateSwaggerDocumentResponse(req));
}</code></pre>

And the Swagger UI document:

<pre class="wp-block-code"><code>&#91;FunctionName("SwaggerUI")]
&#91;SwaggerIgnore]
public static Task&lt;HttpResponseMessage> RunUI(
    &#91;HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "/")]
        HttpRequestMessage req,
    ILogger log,
    &#91;SwashBuckleClient] ISwashBuckleClient swashBuckleClient)
{
  return Task.FromResult(swashBuckleClient.CreateSwaggerUIResponse(req, "json"));
}</code></pre>

Running this locally will provide two endpoints: <figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-2-1024x178.png" alt="" class="wp-image-672" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-2-1024x178.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-2-300x52.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-2-768x134.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> <figure class="wp-block-image"><img src="https://dfar.io/wp-content/uploads/2019/10/image-5-1024x145.png" alt="" class="wp-image-675" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-5-1024x145.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-5-300x43.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-5-768x109.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /></figure> 

The final step for initial configuration is changing the documentation for the API page. Add the following to `host.json`:

<pre class="wp-block-code"><code>{
  "version": "2.0",
  "extensions": {
    ...

    "Swashbuckle": {
      "Documents": [
        {
          "Title": "YOUR_TITLE",
          "Version": "v1",
          "Description": "YOUR_DESCRIPTION"
        }
      ]
    }
  }
}</code></pre>

Which will give you:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-6-1024x166.png" alt="" class="wp-image-680" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-6-1024x166.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-6-300x49.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-6-768x124.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Further Reading:

<https://medium.com/@yuka1984/open-api-swagger-and-swagger-ui-on-azure-functions-v2-c-a4a460b34b55>