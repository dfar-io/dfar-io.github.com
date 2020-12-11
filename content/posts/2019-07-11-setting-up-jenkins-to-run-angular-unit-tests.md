---
title: Setting up Jenkins to Run Angular Unit Tests
author: dfar

date: 2019-07-11T14:12:00+00:00
url: /setting-up-jenkins-to-run-angular-unit-tests/
categories:
  - Uncategorized

---
To be able to run unit tests in a Linux-based Jenkins instance, you just need to SSH into the Jenkins instance and run the following command:

<pre class="wp-block-code"><code>wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb</code></pre>

When installing, you may run into a dependency issue, if so, run:

<pre class="wp-block-code"><code>sudo apt-get install -f
sudo dpkg -i google-chrome-stable_current_amd64.deb</code></pre>

After Google Chrome is installed, you should be able to run `npm test`, meaning you can use Jenkins to run unit tests in the CI process.