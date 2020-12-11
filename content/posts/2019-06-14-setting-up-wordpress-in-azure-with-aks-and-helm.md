---
title: Setting up WordPress in Azure with AKS and Helm
author: dfar

date: 2019-06-14T04:40:48+00:00
url: /setting-up-wordpress-in-azure-with-aks-and-helm/
categories:
  - Uncategorized

---
To get started, you&#8217;ll need the following:

  * kubectl
  * helm
  * Azure subscription

## Installing WordPress in AKS with Helm

Set up an Azure Kubernetes Service with the following:

  * Node size &#8211; desired VM size (cheapest available is B2s)
  * Node count &#8211; need to use at least a minimum of 1.

After AKS is set up, connect to it with the following:

`az aks get-credentials --resource-group AKS_RG --name AKS_NAME`

Install Helm:

`helm init`

Create a `values.yml` file:

<pre class="wp-block-code"><code>wordpressUsername: sammy
wordpressEmail: sammy@example.com
wordpressFirstName: Sammy
wordpressLastName: the Shark
wordpressBlogName: Sammy's Blog!</code></pre>

Create a second file `values.secret.yml`:

<pre class="wp-block-code"><code>wordpressPassword: password</code></pre>

Now install WordPress with the following commands:

`helm install --name blog -f values.yml -f values.secrets.yml stable/wordpress`

After installation, run the following command to get the external IP:

`kubectl get svc --namespace default -w blog-wordpress`

Once the IP is ready, you&#8217;ll likely need to wait for the pods to finish loading. You can check their status with:

`kubectl get pods`

Once ready, try accessing the IP address to get the WordPress installation.

Reference: <https://www.digitalocean.com/community/tutorials/how-to-set-up-wordpress-with-mysql-on-kubernetes-using-helm>