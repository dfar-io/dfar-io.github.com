---
title: Viewing Traffic to Azure VMs using NSG Flow Logs
author: dfar

date: 2019-08-19T19:13:37+00:00
url: /viewing-traffic-to-azure-vms-using-nsg-flow-logs/
categories:
  - Uncategorized

---
Setting up NSG flow logs allows for viewing the traffic coming in through a network security group. This can be useful for a few things:

  * Troubleshooting access issues (maybe something shouldn&#8217;t have access, or vise versa).
  * Providing logging on the traffic accessing a server.

You&#8217;ll need the following to get started with this guide:

  * Ensure that a <a rel="noreferrer noopener" aria-label="network watcher (opens in a new tab)" href="https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-portal" target="_blank">network watcher</a> is configured.
  * An Azure subscription with the <a rel="noreferrer noopener" aria-label="Insights provider (opens in a new tab)" href="https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-portal" target="_blank">Insights provider</a> installed.
  * An existing Network Security Group.
  * A storage account (ideally stored in the same resource group) that will hold the log data.

## Configuration

Go into Network Watcher and click on &#8216;NSG Flow Logs&#8217;:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-1.png" alt="" class="wp-image-578" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-1.png 399w, https://40.76.37.251/wp-content/uploads/2019/08/image-1-300x274.png 300w" sizes="(max-width: 399px) 85vw, 399px" /> </figure> 

Turn on Flow logs, and select the storage account to store logs in. A few notes here:

  * If retention is kept at 0, all logs will stay in the storage account forever. Useful for audits, but will end up costing more in the long run. (I personally set to 7 days).<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-2-756x1024.png" alt="" class="wp-image-579" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-2-756x1024.png 756w, https://40.76.37.251/wp-content/uploads/2019/08/image-2-222x300.png 222w, https://40.76.37.251/wp-content/uploads/2019/08/image-2-768x1040.png 768w, https://40.76.37.251/wp-content/uploads/2019/08/image-2.png 975w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> </figure> 

## Accessing Logs

For viewing the logs, you can either use the Azure Portal or use the [Microsoft Azure Storage Explorer][1].

View the **insights-logs-networksecuritygroupflowevent** container in the configured storage account.

Access the PT1H.json file.<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-3.png" alt="" class="wp-image-580" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-3.png 999w, https://40.76.37.251/wp-content/uploads/2019/08/image-3-300x110.png 300w, https://40.76.37.251/wp-content/uploads/2019/08/image-3-768x282.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

The number associates to the following:

  1. Timestamp
  2. Inbound IP (coming in from Internet)
  3. Outbound IP (going through the NSG)
  4. Inbound Port
  5. Outbound Port
  6. Protocol
  7. Traffic Flow (I &#8211; Inbound, O &#8211; Outbound)
  8. Acceptance (A &#8211; Allowed, D &#8211; Denied)

## Reference

<https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-portal>

 [1]: https://azure.microsoft.com/en-us/features/storage-explorer/