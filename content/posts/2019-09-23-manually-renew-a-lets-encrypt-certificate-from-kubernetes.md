---
title: Manually Renew A Let’s Encrypt Certificate from Kubernetes
author: dfar

date: 2019-09-23T15:10:25+00:00
url: /manually-renew-a-lets-encrypt-certificate-from-kubernetes/
categories:
  - Uncategorized

---
I work with a series of Kubernetes clusters that are restricted to public access, but still are encrypted using Let&#8217;s Encrypt certificates. Normally, when renewal is required, this process is automatically done for you.

In the case above, I have to kick off the renewal process directly. This can be done easily with deleting the TLS secret generated for the certificate. 

First, make sure there is an open port to allow for port 80 to connect to Let&#8217;s Encrypt servers.

This can be done through the UI:<figure class="wp-block-image size-large">

<img src="https://dfar.io/wp-content/uploads/2020/02/image-4-1024x189.png" alt="" class="wp-image-1057" srcset="https://40.76.37.251/wp-content/uploads/2020/02/image-4-1024x189.png 1024w, https://40.76.37.251/wp-content/uploads/2020/02/image-4-300x55.png 300w, https://40.76.37.251/wp-content/uploads/2020/02/image-4-768x142.png 768w, https://40.76.37.251/wp-content/uploads/2020/02/image-4-1536x284.png 1536w, https://40.76.37.251/wp-content/uploads/2020/02/image-4-2048x378.png 2048w, https://40.76.37.251/wp-content/uploads/2020/02/image-4-1200x222.png 1200w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Or via CLI:

<pre class="wp-block-code"><code>kubectl delete secret tls-secret --namespace ingress-nginx</code></pre>

Once this is done, the cert should automatically attempt a renewal. You can check the status of this with:

`kubectl describe certificate tls-secret --namespace ingress-nginx`

You should see the following:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-9-1024x46.png" alt="" class="wp-image-643" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-9-1024x46.png 1024w, https://40.76.37.251/wp-content/uploads/2019/09/image-9-300x14.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-9-768x35.png 768w, https://40.76.37.251/wp-content/uploads/2019/09/image-9.png 1872w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure>