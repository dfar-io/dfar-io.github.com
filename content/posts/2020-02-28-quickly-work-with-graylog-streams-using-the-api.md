---
title: Quickly Work With Graylog Streams Using the API
author: dfar

date: 2020-02-28T17:09:47+00:00
url: /quickly-work-with-graylog-streams-using-the-api/
categories:
  - Technology

---
When using Graylog, you may run into a case where you&#8217;re making a large number of streams to organize data. A quick way to do this is to use the API to programmatically add the streams to the system quickly.

## Authorization

For authorization, you can use Basic Auth with your username and password.

You can also create an access token to allow access for individual systems.

## Interact with API

Use the GET endpoint to get the streams payload:

<pre class="wp-block-preformatted">https://YOUR_GRAYLOG_ENDPOINT/api/streams</pre>

Grab one of the streams and format the payload to use for the POST endpoint, and then insert the streams using the API.

You can check the API browser for more endpoints at `https://GRAYLOG_ENDPOINT/api/api-browser`.