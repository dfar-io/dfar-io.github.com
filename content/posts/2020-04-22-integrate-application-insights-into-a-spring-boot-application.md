---
title: Integrate Application Insights into a Spring Boot Application
author: dfar

date: 2020-04-23T00:00:00+00:00
url: /integrate-application-insights-into-a-spring-boot-application/
categories:
  - Technology

---
To get started, we&#8217;ll set up a basic Spring Boot application, and then add Application Insights in the next step.

## Creating a Skeleton Spring Boot App

To get started, go to <a rel="noreferrer noopener" href="https://start.spring.io/" target="_blank">Spring Boot Initializr</a> and create an app with the following selected:

  * Create a **gradle** project
  * Add the **Spring Web** dependency

Once that&#8217;s done, extract the archive file given and open in your Java IDE of choice (IntelliJ, for example).

Add the following Controller `ApiController.java`to add an API endpoint:

<pre class="wp-block-code"><code>package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ApiController {

    @GetMapping("/")
    public String doGet() {
        return "Hello World!";
    }
}
</code></pre>

Start the application using `clean`, `build`, and `bootRun`. You can verify the endpoint is working with a tool such as Insomnia and calling at `http://localhost:8080` to have &#8220;Hello World!&#8221; displayed.

Once you have this working, you&#8217;re ready to start integrating Application Insights to provide analytics.

## Integrating Application Insights

First, add the Application Insights dependencies to your `build.gradle` file:

<pre class="wp-block-code"><code>dependencies {
    ...  // other dependencies
    compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
}</code></pre>

Then add the `Resources/ApplicationInsights.xml` file:

<pre class="wp-block-code"><code>&lt;?xml version="1.0" encoding="utf-8"?>
&lt;ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   &lt;!-- HTTP request component (not required for bare API) -->
   &lt;TelemetryModules>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   &lt;/TelemetryModules>

   &lt;!-- Events correlation (not required for bare API) -->
   &lt;!-- These initializers add context data to each event -->
   &lt;TelemetryInitializers>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      &lt;Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   &lt;/TelemetryInitializers>

&lt;/ApplicationInsights>
</code></pre>

Finally, you&#8217;ll add the section in startup to add the App Insights Instrumentation Key to the codebase, to link the resource to send data to. A quick note on this &#8211; you can add the key to the .xml file, but I prefer to add it in as an environment variable, so this can be changed across different environments when deployed.

Add the following method to the `DemoApplication.java` file:

<pre class="wp-block-code"><code>private static final Logger log = LoggerFactory.getLogger(DemoApplication.class);

...

@PostConstruct
	private void init() {
		String appInsightsKey = System.getenv("AppInsightsKey");
		if (appInsightsKey == null) {
			log.warn("App Insights Key not provided, no analytics will report.");
			return;
		}

		TelemetryConfiguration.getActive().setInstrumentationKey(appInsightsKey);
	}</code></pre>

## Verifying in App Insights

With the changes made in place, the last step is verifying everything is in place. To start the application with App Insights enabled:

  * Add the App Insights Instrumentation Key to an environment variable called `AppInsightsKey`.
  * Start the application.
  * Call the endpoint at `http://localhost:8080`
  * View the results at the App Insights screen.

## Further Reading

<https://docs.microsoft.com/en-us/azure/azure-monitor/app/java-get-started?tabs=maven>