---
title: Setting up Graylog in Azure
author: dfar

date: 2020-02-20T19:58:22+00:00
url: /setting-up-graylog-in-azure/
categories:
  - Technology

---
To get started with installing Graylog, do the following:

Create a VM using the following:

  * Image: Ubuntu 18.04 LTS
  * Minimum Size: B2s (~$30/month)
  * Open ports 80,443,22

SSH into the server and follow <a rel="noreferrer noopener" aria-label="this guide (opens in a new tab)" href="https://docs.graylog.org/en/3.1/pages/installation/os/ubuntu.html" target="_blank">this guide</a> to get Graylog installed.

To set up public access, set the following variables in Graylog config file:

<pre class="wp-block-code"><code>http_bind_address = PRIVATE_IP
http_external_uri = http://&lt;PUBLIC_IP>/</code></pre>

Once fully installed, set up an Apache reverse proxy:

<pre class="wp-block-code"><code>sudo apt-get install apache2 -y
sudo a2enmod proxy_http proxy_ajp rewrite deflate headers proxy_balancer proxy_connect proxy_html ssl lbmethod_byrequests slotmem_shm proxy</code></pre>

Edit `/etc/apache2/sites-enabled/000-default.conf`:

<pre class="wp-block-code"><code>ProxyPass "/"  "http://PRIVATE_IP:9000/"
ProxyPassReverse "/"  "http://PRIVATE_IP:9000/"</code></pre>

Then restart both servers:

<pre class="wp-block-code"><code>sudo systemctl restart graylog-server.service
sudo systemctl restart apache2</code></pre>

To verify installation, access at <IP_ADDRESS> to verify the installation. If you see the Graylog login screen, you&#8217;ve successfully set up the server.

Finish by setting the SSH networking rule to a trusted IP to improve security.

## Set up HTTPS using Let&#8217;s Encrypt

To set up HTTPS using Let&#8217;s Encrypt, use the <a rel="noreferrer noopener" aria-label="Certbot (opens in a new tab)" href="https://certbot.eff.org/lets-encrypt/ubuntubionic-apache" target="_blank">Certbot</a> directions.

Once that&#8217;s done, make sure to change `http_external_uri` in the Graylog config file and restart Graylog.

## Send Kubernetes Logs to Graylog

First, SSH into the server and configure Elasticsearch (`/etc/elasticsearch/elasticsearch.yml`) to bind to the private IP of the VM:

<pre class="wp-block-code"><code>network.host: PRIVATE_IP</code></pre>

Restart Elasticsearch, then configure Graylog to listen to the new Elasticsearch host:

<pre class="wp-block-code"><code>elasticsearch_hosts = http://PUBLIC_IP:9200</code></pre>

Restart Graylog, then open the firewall to allow for port 9200 to be accessible by the cluster IP. Confirm access by trying to hit port 9200.

Next, <a rel="noreferrer noopener" aria-label="set up an input in Graylog (opens in a new tab)" href="https://docs.fluentd.org/how-to-guides/graylog2#prepare-graylog-for-fluentd" target="_blank">set up an input in Graylog</a>.

After that, create the RBAC role for the cluster (_fluentd-rbac.yml_):

<pre class="wp-block-code"><code>apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system

---

apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch

---

kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-system
</code></pre>

Then create the daemonset, changing the container environment variables as needed (_fluentd-daemonset.yml_):

<pre class="wp-block-code"><code>apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
    version: v1
spec:
  selector:
    matchLabels:
      k8s-app: fluentd-logging
      version: v1
  template:
    metadata:
      labels:
        k8s-app: fluentd-logging
        version: v1
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1.4-debian-elasticsearch
        env:
          - name:  FLUENT_ELASTICSEARCH_HOST
            value: "YOUR_GRAYLOG_HOST"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
          # Custom values for each cluster
          - name: FLUENT_ELASTICSEARCH_LOGSTASH_PREFIX
            value: "YOUR_LOG_PREFIX"
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers</code></pre>

Deploy both of these out:

<pre class="wp-block-code"><code>kubectl create -f fluentd-rbac.yml
kubectl create -f fluentd-daemonset.yml</code></pre>

Then check the logs of the daemonset to confirm correct connection:

<pre class="wp-block-code"><code>kubectl get pods -n kube-system
kubectl logs FLUENTD_POD -n kube-system</code></pre>

## Sending Azure App Service Logs to Graylog

To get started, create the following:

  * Azure Function App
  * Azure Event Hubs

For the App Service in place, enable &#8220;Diagnostic logs&#8221; and send them to the Event Hub.

TO BE CONTINUED

## Increase Heap Size

To increase the heap size, edit `/etc/default/graylog-server`, then restart `graylog-server.service`.

## References

<https://mherman.org/blog/logging-in-kubernetes-with-elasticsearch-Kibana-fluentd/#fluentd>