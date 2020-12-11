---
title: Adding SSL to an AKS Ingress Controller (without using Let’s Encrypt)
author: dfar

date: 2019-08-12T19:34:44+00:00
url: /adding-ssl-to-an-aks-ingress-controller-without-using-lets-encrypt/
categories:
  - Uncategorized

---
I wrote about the ability to add HTTPS to an AKS cluster using Let&#8217;s Encrypt, but recently ran into a case where I needed to add a cert from a specific CA to the cluster.

To do this, you need the following:

  * An AKS cluster deployed in an Azure tenant.
  * A certificate (should start with &#8212;&#8211;BEGIN CERTIFICATE&#8212;&#8211;)
  * A private key associated to the certificate above (used when creating the CSR for the cert, and will start with &#8212;&#8211;BEGIN RSA PRIVATE KEY&#8212;&#8211;)

Import the cert into the cluster:

<pre class="wp-block-code"><code>kubectl create secret tls tls-ca-secret --key certificate.key --cert certificate.crt</code></pre>

After the cert imported, create an ingress controller:

<pre class="wp-block-code"><code>apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.org/client-max-body-size: '10m'
    nginx.ingress.kubernetes.io/enable-cors: 'true'
spec:
  tls:
    - hosts:
        - YOUR_DOMAIN
      secretName: tls-ca-secret
  rules:
  - host: YOUR_DOMAIN
      http:
        paths:
          - path: /some/endpoint
            backend:
              serviceName: some-service
              servicePort: 80</code></pre>

Afterwards, check to ensure your cert is coming through using the endpoint defined in the Ingress Controller.