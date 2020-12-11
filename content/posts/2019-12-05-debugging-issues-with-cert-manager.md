---
title: Debugging Issues With cert-manager
author: dfar

date: 2019-12-05T15:07:24+00:00
url: /debugging-issues-with-cert-manager/
categories:
  - Technology

---
If you&#8217;re using cert-manager to get a Let&#8217;s Encrypt certificate for your Kubernetes cluster and running into issues, you can do the following to see what might be going on:

## Check Status of Challenge

First, check the status of the certificate:

<pre class="wp-block-code"><code>kubectl describe certificate tls-secret --namespace ingress-nginx</code></pre>

You&#8217;ll see something that says something like **Created new CertificateRequest resource &#8220;tls-secret-1764787185&#8221;**. Run the following to get more information:

<pre class="wp-block-code"><code>kubectl describe CertificateRequest tls-secret-1764787185 --namespace ingress-nginx</code></pre>

This command will provide the ID of an order, so use a similiar command:

<pre class="wp-block-code"><code>kubectl describe Order tls-secret-1764787185-845386587 --namespace ingress-nginx</code></pre>

Finally, you will get a Challenge to view:

<pre class="wp-block-code"><code>kubectl describe Challenge tls-secret-1764787185-845386587-4119551803 --namespace ingress-nginx</code></pre>

## View Logs

If the above isn&#8217;t helping, you can view the logs:

<pre class="wp-block-code"><code>kubectl logs -n cert-manager deploy/cert-manager -f</code></pre>