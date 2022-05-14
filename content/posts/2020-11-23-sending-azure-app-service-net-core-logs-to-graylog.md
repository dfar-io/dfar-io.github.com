---
title: Sending Azure App Service .NET Core Logs to Graylog
author: dfar

date: 2020-11-23T18:09:25+00:00
url: /sending-azure-app-service-net-core-logs-to-graylog/
categories:
  - Technology

---
## Pre-requisites

You&#8217;ll need:

  * A Graylog instance running to send logs to, which should be running a GELF UDP input.
  * A .NET Core application to send logs from.
  * An Azure App Service to host the above application.

## Procedure 

First, add the following packages to your application:

  * Serilog.AspNetCore
  * Serilog.Sinks.Graylog

Next, add the following to `Program.cs`, at the bottom of the CreateDefaultBuilder method:

```
.UseSerilog((hostingContext, loggerConfiguration) =>
             loggerConfiguration.ReadFrom
.Configuration(hostingContext.Configuration));
```

Next, configure the `appsettings.json`file, cleaning the existing Logging config and adding the following:

```
"Serilog": {
    "Using": &#91; "Serilog.Sinks.Graylog" ],
    "MinimumLevel": "Information",
    "WriteTo": &#91;
      {
        "Name": "Graylog",
        "Args": {
          "hostnameOrAddress": "GRAYLOG_INSTANCE_URL",
          "port": "12201",
          "transportType": "Udp"
        }
      }
    ],
    "Properties": {
      "Application": "APPLICATION_NAME"
    }
  },
```

Do this for the `appsettings.Development.json` file to allow console logging when in Development:

```
"Serilog": {
    "Using": &#91; "Serilog.Sinks.Console" ],
    "MinimumLevel": "Information",
    "WriteTo": &#91;
      { "Name": "Console" }
    ],
    "Application": "APPLICATION_NAME"
  }
```

## Verification

Testing the application locally, check to see that you have console logging activated.

Once working, deploy the application to your Azure App Service and verify logs are being sent to Graylog.

To change the Application name, set the &#8220;Serilog:Properties:Application&#8221; configuration value.
