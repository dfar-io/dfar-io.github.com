---
title: Access Artifactory Securely with Kubernetes
author: dfar

date: 2020-06-30T18:17:53+00:00
url: /access-artifactory-securely-with-kubernetes/
categories:
  - Technology

---
To allow Kubernetes to download images from a secured Artifactory instance (for example, an instance that doesn&#8217;t allow anonymous access):

## Artifactory User Setup

  1. Create a user in Artifactory to serve as the service principal.
  2. Log into this user and create an API token.

## Docker Credentials

With the API token, log into the Docker instance (username is service account username, password is API key):

<pre class="wp-block-code"><code>docker login REPOSITORY_PATH</code></pre>

Once logged in successfully, access the `config.json` file created to get the token to use.

## Kubernetes Token Creation

Upload the following JSON, using the auth token generated above:

<pre class="wp-block-code"><code>kubectl create secret generic artifactory-authtoken \
    --from-file=.dockerconfigjson=&lt;path/to/.docker/config.json> \
    --type=kubernetes.io/dockerconfigjson</code></pre>

## Kubernetes Deployment Reference

Finally, reference the auth token in your Kubernetes deployments:

<pre class="wp-block-code"><code>spec:
  ..
  imagePullSecrets:
  - name: artifactory-authtoken</code></pre>

## Troubleshooting

If you&#8217;re having issues, make sure the server name used in the deployment and the server name logged in (including port numbers) match.

## Reference

<https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/>