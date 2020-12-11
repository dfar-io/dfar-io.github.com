---
title: Connecting An Azure Function App to Loggly
author: dfar

date: 2020-06-16T00:53:43+00:00
url: /connecting-an-azure-function-app-to-loggly/
categories:
  - Technology

---
Doing the following will get a Function App hooked up to Loggly. 

  1. Create an Event Hub namespace, and an Event Hub.
  2. Create a Function App, and an Event Hub function.
  3. Create a Loggly HTTP/S Event Endpoint, and get the URL endpoint.
  4. Update the Event Hub trigger with <a rel="noreferrer noopener" href="https://gist.github.com/dfar-io/699d6de582a6ba101bc6324a841faee8" target="_blank">this code</a> (use test to verify)
  5. Create a HTTP Trigger function.
  6. In the Function App&#8217;s diagnostic settings, turn on FunctionAppLogs and stream to the Event Hub.
  7. Call the HTTP trigger endpoint and verify results in Loggly.

## Reference

<https://www.loggly.com/blog/how-to-stream-logs-from-azure-resources-to-loggly/>