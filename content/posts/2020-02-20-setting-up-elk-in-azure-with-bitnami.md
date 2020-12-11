---
title: Setting up ELK in Azure with Bitnami
author: dfar

date: 2020-02-20T16:49:49+00:00
url: /setting-up-elk-in-azure-with-bitnami/
categories:
  - Technology

---
To get started with using ELk in Azure, do the following:

Create a VM using the &#8216;_ELK Certified by Bitnami_&#8216; image.

Next, you&#8217;ll need to get the credentials to log into the application &#8211; look into the serial log of Boot Diagnostics of the VM to access the username and password. If needed, <a rel="noreferrer noopener" aria-label="a guide is available (opens in a new tab)" href="https://docs.bitnami.com/azure/faq/get-started/find-credentials/" target="_blank">a guide is available</a>. 

Access the public IP of the VM created, and log in using the credentials above. Kibana should open up and allow for importing sample data.

Once set up, the first thing to do is reset the default password &#8211; you can do that with:

<pre class="wp-block-code"><code>sudo htpasswd -c /opt/bitnami/kibana/conf/password user</code></pre>

<a rel="noreferrer noopener" aria-label="More information here (opens in a new tab)" href="https://docs.bitnami.com/azure/apps/elk/configuration/change-default-password/" target="_blank">More information here</a>.

## Send Logs from AKS

To send logs from AKS, <a rel="noreferrer noopener" aria-label="set up Fluentd (opens in a new tab)" href="https://mherman.org/blog/logging-in-kubernetes-with-elasticsearch-Kibana-fluentd/" target="_blank">set up Fluentd</a>, **making sure to open port 9200 to the cluster IP**.

## Set up Azure AD Authentication

To set up Azure AD Authentication, an Azure AD Premium subscription is required. [A guide can be found at Elastic&#8217;s site.][1]

 [1]: https://www.elastic.co/blog/saml-based-single-sign-on-with-elasticsearch-and-azure-active-directory