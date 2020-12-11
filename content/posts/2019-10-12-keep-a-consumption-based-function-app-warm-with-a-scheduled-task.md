---
title: Keep A Consumption-Based Function App Warm With A Scheduled Task
author: dfar

date: 2019-10-12T16:14:40+00:00
url: /keep-a-consumption-based-function-app-warm-with-a-scheduled-task/
categories:
  - Uncategorized

---
With Azure Function Apps using a Consumption plan, they will need to be warmed up if not used for 20 minutes to prevent having cold starts for the users in place. If you&#8217;re serving an API using a Function app, you&#8217;ll want to put this in place to keep performance ideal.

Something to note with this solution &#8211; it works well for low-traffic APIs where the goal is to serve an API using the consumption app for low costs. Assuming larger traffic use, you may be better off switching to a dedicated App Service plan, to prevent the cold start issue at all, because cold starts will still come when scaling out.

To follow this guide, I&#8217;ll assume you already have a Function app in place. Create a new function with the following:

<pre class="wp-block-code"><code>using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace YOUR_NAMESPACE
{
  public static class KeepWarm
  {
    [FunctionName("KeepWarm")]
    public static void Run([TimerTrigger("0 */15 * * * *")]TimerInfo myTimer, ILogger log)
    {
      log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    }
  }
}
</code></pre>

That&#8217;s it! After this is deployed, the cold start issue of your API should be removed, as running the log function every 15 minutes will prevent the system from needing a cold start.

## Further Reading

<https://mikhail.io/2018/05/azure-functions-cold-starts-beyond-first-load/>