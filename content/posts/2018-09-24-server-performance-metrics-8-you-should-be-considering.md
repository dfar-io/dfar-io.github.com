---
title: 'Server Performance Metrics: 8 You Should Be Considering'
author: dfar

date: 2018-09-24T20:03:09+00:00
excerpt: |
  
  <![CDATA[]]>
url: /server-performance-metrics-8-you-should-be-considering/
categories:
  - Uncategorized

---
<!--[CDATA[

<p-->This post was originally featured at 

[Raygun][1].

<span style="font-weight: 400;">With the DevOps movement entering the spotlight, more and more developers concern themselves with the&nbsp;end-to-end delivery of web applications. This includes the deployment, performance, and maintenance of the application.</span>

<span style="font-weight: 400;">As an application gains more users in a production environment, it&#8217;s increasingly critical that you understand the role of the server. To determine the health of your applications, you may find it useful to gather performance metrics for the servers running your web applications.</span>

<span style="font-weight: 400;">All different types of web servers&nbsp;(like Apache, IIS, Azure, AWS, and NGINX, for example) have similar server performance metrics. Most of my experience in this realm lies in Microsoft Azure, which provides an easy-to-use interface for finding and collecting data. Working with Microsoft Azure gives the capability to host applications in either Azure App Services (PaaS), or Azure Virtual Machines (IaaS). This setup gets you a view of the different metrics for the application or server running.</span>

<span style="font-weight: 400;">Because of all this experience I&#8217;ve had in the last few months, I&#8217;ve found what I think is eight of the most useful server performance metrics. These metrics can be divided into two categories: **app performance metrics and user experience metrics.</span>

<span style="font-weight: 400;">Let&#8217;s start by looking at the metrics under the app performance umbrella.</span>

## **App performance metrics**

<span style="font-weight: 400;">App performance metrics are specific to the speed of the web applications that are running. If you&#8217;re having issues with an application performing slowly, these metrics are a good place to start.</span>

### **Metric 1: Requests per second**

**Requests per second**<span style="font-weight: 400;">&nbsp;(also called throughput) is just like it sounds—it&#8217;s the number of requests your server receives every second. This is a fundamental metric that measures the main purpose of a web server, which is receiving and processing requests. Large-scale applications can reach </span>[<span style="font-weight: 400;">up to about 2,000 requests</span>][2] <span style="font-weight: 400;">per second.</span>

<span style="font-weight: 400;">Given enough load, any server can fall. When considering the impact, remember that requests are just that: a single request to the server. This metric doesn&#8217;t consider what&#8217;s happening in each of these requests.</span>

<span style="font-weight: 400;">This brings us to our next metric.</span>

### **Metric 2: Data in and data out**

<span style="font-weight: 400;">The next metric I suggest you look at is your data in and data out. The </span>**data in** <span style="font-weight: 400;">metric is the size of the request payload going to the web server. For this metric, a lower rate is better (lower meaning that small payloads being sent into the server). A high data-in measurement can indicate the application is requesting more information than it needs.</span>

**Data out** <span style="font-weight: 400;">is the response payload being sent to clients. As </span>[<span style="font-weight: 400;">websites have been getting larger over time</span>][3]<span style="font-weight: 400;">, this causes an issue especially for those with slower network connections. Bloated response payloads lead to slow websites, and slow websites will dissatisfy your users. With enough slowness, these users abandon the website and move on. Google suggests </span>[<span style="font-weight: 400;">pages that take three or more seconds for mobile users to load have about a 53% chance of users abandoning</span>][4] <span style="font-weight: 400;">before load completion.</span>

### **Metric 3: Average response time**

<span style="font-weight: 400;">Defined directly, the </span>**average response time (ART)** <span style="font-weight: 400;">is the average time the server takes to respond to all requests given to it. This metric is a strong indicator of the overall performance of the application, giving an impression of the application usability. In general, the lower this number is, the better. But there are </span>[<span style="font-weight: 400;">studies showing that the ceiling limit for a user navigating through an application is around one second</span>][5]<span style="font-weight: 400;">.</span>

<span style="font-weight: 400;">When considering ART, remember what the acronym&nbsp;stands for—it&#8217;s just an average. Like all metrics determined with an average, high outliers can throw the number off completely and make the system seem slower than is. ART is most helpful when used alongside our next metric on the list.</span>

### **Metric 4: Peak response time**

<span style="font-weight: 400;">Similar to the average response time, the </span>**peak response time (PRT)** <span style="font-weight: 400;">is the measurement of the longest responses for all requests coming through the server. This is a good indicator of performance pain points in the application.</span>

<span style="font-weight: 400;">PRT will not only give you an idea of which portions of your applications are causing hangups; it will also help you find the root cause of these hangups. For example, if there&#8217;s a certain slow web page&nbsp;or a particularly slow call, this metric can give you an&nbsp;idea of where to look.</span>

### **Metric 5: Hardware utilization**

<span style="font-weight: 400;">Next, let&#8217;s talk about overall hardware utilization. Any application or server running is limited by the resources allocated to it. Therefore, keeping track of the utilization of resources is key, primarily to determine if a resource bottleneck exists. You have three major aspects of a server to consider:</span>

<li style="font-weight: 400;">
  <span style="font-weight: 400;">the processor</span>
</li>
<li style="font-weight: 400;">
  <span style="font-weight: 400;">the RAM (memory)</span>
</li>
<li style="font-weight: 400;">
  <span style="font-weight: 400;">the disk space and usage</span>
</li>

<span style="font-weight: 400;">When considering these, you&#8217;re looking for what can become a bottleneck for the whole system. As any physical (or virtual!) computer running with these components will show, performance is only as strong as its weakest link. This metric can tell you what the bottleneck is and what physical component can be updated to improve performance.</span>

<span style="font-weight: 400;">For example, you may run into issues when trying to render data from a physical hard drive. That will cause a bottleneck in the I/O interactions between gathering files and presenting them to the user. While the hard drive spins and gathers data, the other physical components do nothing. An upgrade to a solid-state drive would improve the performance of the entire application because the bottleneck will disappear.</span>

### **Metric 6: Thread count**

<span style="font-weight: 400;">The next metric—the thread count of a server—tells you how many concurrent requests are happening in the server at a particular time. This metric will help you understand what the general load of a server looks like from a request level. It will also give you an idea of the load placed on the server when running multiple threads.</span>

<span style="font-weight: 400;">A server can generally be configured with a maximum thread count allowed. By doing this, you&#8217;re setting a max limit of requests that can happen at one time. If the thread count passes this maximum value, all remaining requests will be deferred until there&#8217;s space available in the queue to process them. If these deferred requests take too long, they&#8217;ll generally time out.</span>

<span style="font-weight: 400;">It&#8217;s worth noting that increasing the max thread count generally relies on having the appropriate resources available for use.</span>

## **User experience metrics**

<span style="font-weight: 400;">Now that we&#8217;ve covered the app performance metrics, let&#8217;s discuss a few that are user experience centered. These server performance metrics can measure your users&#8217; overall satisfaction when using your web applications.</span>

### **Metric 7: Uptime**

<span style="font-weight: 400;">Although not directly related to its performance, the uptime of the server is a critical metric. Uptime is the percentage that the server is available for use. Ideally, you&#8217;re aiming for 100% uptime, and you&#8217;ll see many cases of 99.9% uptime (or more) when looking at web hosting packages. It&#8217;s not uncommon for software projects to abide by a service level agreement that dictates a particular server uptime rate.</span>

<span style="font-weight: 400;">If uptime metrics checking isn&#8217;t something your server can provide built in, there are plenty of third-party services, such as </span>[<span style="font-weight: 400;">Updown.io</span>][6]<span style="font-weight: 400;">, that can do it for you. These services can even give you a visual depiction of their report:</span>

<figure class="wp-block-image">

<img src="http://40.76.37.251/wp-content/uploads/2018/09/uptime-monitoring.png" alt="" class="wp-image-205" /> </figure> 

And here&#8217;s an interesting fact.&nbsp;[Calculating the monthly allowed downtime][7] shows

  * 99%: ~7 hours
  * 99.9%:&nbsp; ~45 minutes
  * **99.999%:&nbsp; 30 seconds**

### **Metric 8: HTTP server error rate**

The HTTP server error rate is a performance metric that doesn&#8217;t directly relate to application performance, but it&#8217;s a very critical one. It returns the count of internal server errors (or HTTP 5xx codes) being returned to clients. These errors are returned from malfunctioning applications when you have an exception or other error not being handled correctly.

A good practice is to set up an alert whenever these kinds of errors occur. Because 500 errors are almost completely preventable, you can be certain you have a robust application. Being notified of all HTTP server errors allows you to stay on top of any errors occurring. This prevents the issue of having errors build up in the application over time.

## **How to measure server performance**

Measuring server performance with an Application Performance Monitoring (APM) tool like \[Raygun APM\](https://raygun.com/platform/apm) is the easiest and most accurate way of measuring the health of your software. APM should be, giving your team greater context and diagnostic tools into your biggest application performance questions. Discover and pinpoint the root cause of performance issues with greater speed and accuracy than traditional APM solutions.

## **Keep your finger on the pulse**

These are the server performance metrics I&#8217;ve personally found to be the most valuable. If you collect and monitor this kind data on both your users&#8217; experience and your app performance, very little will fall between the cracks.

Did I mention any metrics that you&#8217;re not currently using? Consider trying them out. After all, metrics are your best way to keep your eye on your server performance—and, by extension, your application&#8217;s health.

]]>

 [1]: https://raygun.com/blog/server-performance-metrics/
 [2]: https://wrongsideofmemphis.wordpress.com/2013/10/21/requests-per-second-a-reference/
 [3]: https://mobiforge.com/research-analysis/the-web-is-doom
 [4]: https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/
 [5]: https://www.nngroup.com/articles/response-times-3-important-limits/
 [6]: https://updown.io/
 [7]: https://uptime.is/