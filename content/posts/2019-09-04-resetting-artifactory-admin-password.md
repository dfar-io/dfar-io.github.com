---
title: Resetting Artifactory Admin Password
author: dfar

date: 2019-09-04T15:28:46+00:00
url: /resetting-artifactory-admin-password/
categories:
  - Uncategorized

---
If you&#8217;ve lost access to the administrator account in your on-premised Artifactory, this guide will help walk you through setting it back up. You&#8217;ll need to have **SSH access** to the server in order to complete this guide.

To get started, SSH into the server and open**`<em>$<code>ARTIFACTORY_HOME/etc/security/access/bootstrap.creds`</em></code>**. Enter the following information:

`access-admin@127.0.0.1=YOUR_PASSWORD`

Now assign read/write permissions:

`<code>chmod&nbsp;600`&nbsp;`bootstrap.creds`</code>

And finally, restart the server.

Once that&#8217;s done, you&#8217;ll need to generate the Basic authorization header:

<pre class="wp-block-code"><code>echo -n access-admin:YOUR_PASSWORD | base64</code></pre>

Then make the following POST call while still SSHed into the server:

<pre class="wp-block-code"><code>curl --request PATCH \
  --url http://localhost:8081/artifactory/api/access/api/v1/users/admin \
  --header 'authorization: Basic &lt;USE_CODE_FROM_ABOVE>' \
  --header 'content-type: application/json' \
  --data '{"password":"NEW_PASSWORD"}'</code></pre>

If you receive a response like the following, you&#8217;re all set:

<pre class="wp-block-code"><code>{
  "username" : "admin",
  "realm" : "internal",
  "status" : "enabled",
  "allowed_ips" : &#91; "*" ],
  "created" : "2019-08-01T13:47:16.959Z",
  "modified" : "2019-09-04T15:25:02.398Z",
  "last_login_time" : "2019-08-21T13:46:55.061Z",
  "last_login_ip" : "204.194.141.30",
  "custom_data" : {
    "updatable_profile" : "true",
    "artifactory_admin" : "true"
  },
  "password_expired" : false,
  "password_last_modified" : 1567610702390,
  "groups" : &#91; ]
}</code></pre>