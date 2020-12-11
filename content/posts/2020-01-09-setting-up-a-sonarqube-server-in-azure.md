---
title: Setting up a SonarQube Server in Azure
author: dfar

date: 2020-01-09T16:39:23+00:00
url: /setting-up-a-sonarqube-server-in-azure/
categories:
  - Technology

---
To get started with using [SonarQube][1] in Azure, do the following.

Create a virtual machine with the **Standard B2s** (~$30/month) size or larger.

SSH into the server and download all pre-reqs:

<pre class="wp-block-code"><code>sudo apt-get update
sudo apt install openjdk-11-jre-headless unzip -y</code></pre>

Create a non-root user:

<pre class="wp-block-code"><code>sudo adduser sonarqube
sudo usermod -aG sudo sonarqube
sudo su - sonarqube</code></pre>

Download and unzip SonarQube (you can get the wget URL from the [Downloads][2] page):

<pre class="wp-block-code"><code>wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.1.0.31237.zip

sudo unzip sonarqube-8.1.0.31237.zip -d /opt/sonarqube

sudo mv /opt/sonarqube/sonarqube-8.1.0.31237/* /opt/sonarqube

sudo chown -R sonarqube /opt/sonarqube</code></pre>

Then start it using the following **as the non-root user**:

<pre class="wp-block-code"><code>/opt/sonarqube/bin/linux-x86-64/sonar.sh console</code></pre>

Wait for the text **SonarQube is up** and then verify access at IP_ADDRESS:9000 (you can log in with admin/admin).

## Set Up PostgreSQL

Next step is setting up a dedicated database &#8211; we will use PostgreSQL to accomplish this.

Install PostgreSQL:

<pre class="wp-block-code"><code>sudo apt-get install postgresql -y</code></pre>

And connect into the server instance and run the following commands:

<pre class="wp-block-code"><code>sudo -u postgres psql postgres

CREATE USER sonarqube WITH PASSWORD 'YOUR_PASSWORD'; CREATE ROLE
CREATE DATABASE sonarqube;
GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonarqube;</code></pre>

Now modify **/opt/sonarqube/conf/sonar.properties**:

<pre class="wp-block-code"><code>sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
sonar.jdbc.username=sonarqube
sonar.jdbc.password=mypassword</code></pre>

Add the following line to **/etc/sysctl.conf:**

<pre class="wp-block-code"><code>vm.max_map_count=262144</code></pre>

To finish, **restart the server**, start up SonarQube again and verify that it can start up successfully, alongside having the &#8220;embedded DB&#8221; warning removed from the login screen.

## Set to Run As A Service On VM Start

Next, we&#8217;ll set up the ability to start SonarQube on VM start by running SonarQube as a service.

Add the following file **/etc/systemd/system/sonarqube.service:**

<pre class="wp-block-code"><code>[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=simple
User=sonarqube
Group=sonarqube
PermissionsStartOnly=true
ExecStart=/usr/bin/nohup /usr/bin/java -Xms32m -Xmx32m -Djava.net.preferIPv4Stack=true -jar /opt/sonarqube/lib/sonar-application-8.1.0.31237.jar
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=8192
TimeoutStartSec=5
Restart=always
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target</code></pre>

Then enable and start the service:

<pre class="wp-block-code"><code>sudo systemctl enable sonarqube.service
sudo systemctl start sonarqube.service</code></pre>

Confirm by restarting the server and checking that SonarQube is running.

## Setting up Reverse Proxy and HTTPS with Let&#8217;s Encrypt

Next step is setting up HTTPS, in this case we&#8217;ll use Let&#8217;s Encrypt for the certificate. To serve the correct ports, we&#8217;ll [set up Apache and serve it as a proxy][3] to the built-in Tomcat server that SonarQube provides.

When looking to configure Apache, set up the pre-reqs:

<pre class="wp-block-code"><code>sudo apt install apache2 -y
sudo a2enmod proxy_http proxy_ajp rewrite deflate headers proxy_balancer proxy_connect proxy_html ssl lbmethod_byrequests slotmem_shm proxy</code></pre>

Add these lines to **/etc/apache2/sites-enabled/000-default.conf:**

<pre class="wp-block-code"><code>ProxyPass / "http://localhost:9000/"
ProxyPassReverse / "http://localhost:9000/"</code></pre>

Restart Apache:

<pre class="wp-block-code"><code>sudo systemctl restart apache2</code></pre>

And check that you can access SonarQube using port 80 (HTTP).

Once port 80 is accessible, you can follow the [Certbot][4] steps to enable HTTPS.

## Next Steps<figure class="wp-block-embed-wordpress wp-block-embed is-type-wp-embed is-provider-dave-farinelli">

<div class="wp-block-embed__wrapper">
  https://dfar.io/setting-up-sonarqube-in-azure-with-azure-ad-integration/
</div></figure>

 [1]: https://www.sonarqube.org/
 [2]: https://www.sonarqube.org/downloads/
 [3]: https://dfar.io/setting-up-artifactory-to-use-a-root-domain-with-a-reverse-proxy/
 [4]: https://certbot.eff.org/lets-encrypt/ubuntubionic-apache