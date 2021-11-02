---
title: "Azure 204 Notes"
date: 2021-11-02T22:38:48Z
draft: false
---

## Create Serverless Applications

* Logic Apps - design first, for Devs, GUI/Code, provides integrations
* Microsoft Power Automate - design first, for BAs/users, GUI only, self-service
* WebJobs - code first, runs program/script automatically, control of JobHost and retry logic
* Azure Function - code first, most versatile, should be default option

### Azure Functions

* Pay-as-you-go - cheap to use
* Stateless
* Event driven (multiple options)
* Limited execution time/frequency (5 mins)
* Can use Consumption plan (serverless) or App Service (dedicated, avoids cold starts)
* Monitoring possible through App. Insights

#### Terminology

* Inputs - data/file provided
* Actions - operations
* Conditions - check if logic
* Output - created data/file
* Triggers - events
* Bindings - connects data/services to function (potentially w/o code)

#### HTTP Trigger Security

* Function - function-specific or admin key
* Admin - admin key only
* Anonymous - no key required

Can be provided by either:

1. HTTP Header `x-functions-key`. (recommended)
1. Query string param `code`.
