---
title: Getting Slack Notifications with Azure Monitor
author: dfar

date: 2019-09-30T00:24:13+00:00
url: /getting-slack-notifications-with-azure-monitor/
categories:
  - Uncategorized

---
An option when trying to get alerts from Azure Monitor (such as web tests, processing alerts and more) is to get them to a Slack channel to allow for an alternative to receiving alerts via email.

## Setting up Slack

To start, you&#8217;ll need to have a Slack workspace with an available app. If needed, create the channel desired to receive notifications.

Create a <a rel="noreferrer noopener" aria-label="new Slack app (opens in a new tab)" href="https://api.slack.com/apps" target="_blank">new Slack app</a> named &#8220;Azure Notifications&#8221; and turn on the &#8220;Incoming Webhooks&#8221; capability, which will provide a URL (webhook) that you&#8217;ll use later to receive the notifications from Azure.

## Creating Conversion Logic App in Azure

Next, you&#8217;ll need to create a logic app in Azure that takes the alert provided from Azure and converts the message into something that can be displayed in Slack.

Create a logic app, open Code View and use the following (replacing the URI provided with the one above):

<pre class="wp-block-code"><code>{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "Http": {
                "inputs": {
                    "body": {
                        "text": "@{if(equals(triggerBody()?['data']?['essentials']?['monitorCondition'],'Resolved'), ':green_heart:', ':red_circle:')} @{triggerBody()?['data']?['essentials']?['alertRule']}"
                    },
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "method": "POST",
                    "uri": "YOUR_WEBHOOK_HERE"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "parameters": {},
        "triggers": {
            "manual": {
                "inputs": {
                    "schema": {
                        "properties": {
                            "data": {
                                "properties": {
                                    "alertContext": {
                                        "properties": {
                                            "condition": {
                                                "properties": {
                                                    "allOf": {
                                                        "items": {
                                                            "properties": {
                                                                "dimensions": {
                                                                    "items": {
                                                                        "properties": {
                                                                            "name": {
                                                                                "type": "string"
                                                                            },
                                                                            "value": {
                                                                                "type": "string"
                                                                            }
                                                                        },
                                                                        "required": [
                                                                            "name",
                                                                            "value"
                                                                        ],
                                                                        "type": "object"
                                                                    },
                                                                    "type": "array"
                                                                },
                                                                "metricName": {
                                                                    "type": "string"
                                                                },
                                                                "metricNamespace": {
                                                                    "type": "string"
                                                                },
                                                                "metricValue": {
                                                                    "type": "number"
                                                                },
                                                                "operator": {
                                                                    "type": "string"
                                                                },
                                                                "threshold": {
                                                                    "type": "string"
                                                                },
                                                                "timeAggregation": {
                                                                    "type": "string"
                                                                }
                                                            },
                                                            "required": [
                                                                "metricName",
                                                                "metricNamespace",
                                                                "operator",
                                                                "threshold",
                                                                "timeAggregation",
                                                                "dimensions",
                                                                "metricValue"
                                                            ],
                                                            "type": "object"
                                                        },
                                                        "type": "array"
                                                    },
                                                    "windowSize": {
                                                        "type": "string"
                                                    }
                                                },
                                                "type": "object"
                                            },
                                            "conditionType": {
                                                "type": "string"
                                            },
                                            "properties": {}
                                        },
                                        "type": "object"
                                    },
                                    "essentials": {
                                        "properties": {
                                            "alertContextVersion": {
                                                "type": "string"
                                            },
                                            "alertId": {
                                                "type": "string"
                                            },
                                            "alertRule": {
                                                "type": "string"
                                            },
                                            "alertTargetIDs": {
                                                "items": {
                                                    "type": "string"
                                                },
                                                "type": "array"
                                            },
                                            "description": {
                                                "type": "string"
                                            },
                                            "essentialsVersion": {
                                                "type": "string"
                                            },
                                            "firedDateTime": {
                                                "type": "string"
                                            },
                                            "monitorCondition": {
                                                "type": "string"
                                            },
                                            "monitoringService": {
                                                "type": "string"
                                            },
                                            "originAlertId": {
                                                "type": "string"
                                            },
                                            "resolvedDateTime": {
                                                "type": "string"
                                            },
                                            "severity": {
                                                "type": "string"
                                            },
                                            "signalType": {
                                                "type": "string"
                                            }
                                        },
                                        "type": "object"
                                    }
                                },
                                "type": "object"
                            },
                            "schemaId": {
                                "type": "string"
                            }
                        },
                        "type": "object"
                    }
                },
                "kind": "Http",
                "type": "Request"
            }
        }
    }
}</code></pre>

You can test by running the code directly, and see if you get a red circle:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-11.png" alt="" class="wp-image-659" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-11.png 494w, https://40.76.37.251/wp-content/uploads/2019/09/image-11-300x75.png 300w" sizes="(max-width: 494px) 85vw, 494px" /> </figure> 

Assuming this is in place, the final step is creating an action group that allows for setting alerts to send to the Slack channel.

Before going to the next step, take note of the webhook provided by the Logic App:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-12.png" alt="" class="wp-image-660" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-12.png 931w, https://40.76.37.251/wp-content/uploads/2019/09/image-12-300x47.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-12-768x120.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Setting up Action Group

The final step is setting up an action group with the Logic App webhook, while turning on the Common Alert Schema.