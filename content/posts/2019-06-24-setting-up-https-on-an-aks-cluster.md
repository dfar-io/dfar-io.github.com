---
title: Setting up HTTPS on an AKS Cluster
author: dfar

date: 2019-06-24T16:16:08+00:00
url: /setting-up-https-on-an-aks-cluster/
categories:
  - Uncategorized

---
## Pre-Reqs

The following is required:

  * An ingress controller should already be installed.
  * The public IP of the Ingress controller should have a DNS name.
  * Helm needs to be running at 2.13.1.
  * The Kubernetes cluster should be publicly accessible (to allow cert creation)

To check if Helm is running with version **2.13.1** (there is a bug that doesn&#8217;t allow 2.14+ to work). To check, run the following:

<pre class="wp-block-code"><code>helm version

Client: &version.Version{SemVer:"v2.13.1", GitCommit:"618447cbf203d147601b4b9bd7f8c37a5d39fbb4", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.13.1", GitCommit:"618447cbf203d147601b4b9bd7f8c37a5d39fbb4", GitTreeState:"clean"}</code></pre>

If the output doesn&#8217;t match as below, you need to downgrade Helm. Install:

<pre class="wp-block-code"><code># only needed if Client above is not 2.13.1
choco uninstall kubernetes-helm
choco install kubernetes-helm --version 2.13.1

helm init --upgrade --force-upgrade</code></pre>

## Procedure

Run the following commands: 

<pre class="wp-block-code"><code># Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install --name cert-manager --namespace cert-manager --version v0.8.0 jetstack/cert-manager</code></pre>

Create the following YAML file \`cluster-issuer.yml&#8217;: 

<pre class="wp-block-code"><code>apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-nginx
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: UPDATE_THIS_EMAIL
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}</code></pre>

Apply the changes to the cluster:

<pre class="wp-block-code"><code>kubectl apply -f ../shared/cluster-issuer.yml</code></pre>

Create the `certificate.yml` file: 

<pre class="wp-block-code"><code>apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-nginx
spec:
  secretName: tls-secret-staging
  dnsNames:
  - YOUR_DOMAIN
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - YOUR_DOMAIN
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer</code></pre>

Apply those changes:

<pre class="wp-block-code"><code>kubectl apply -f certificate.yml</code></pre>

## Verification

Confirm creation of the certificate:

<pre class="wp-block-code"><code>kubectl describe certificate tls-secret --namespace ingress-nginx</code></pre>

You should see the following:

<pre class="wp-block-code"><code>...
Normal   OrderCreated        27s                cert-manager  Created Order resource "tls-secret-3300974441"
Normal   CertIssued          3s (x2 over 20m)   cert-manager  Certificate issued successfully
Normal   OrderComplete       3s                 cert-manager  Order "tls-secret-3300974441" completed successfully</code></pre>

Verify HTTPS can be accessed.