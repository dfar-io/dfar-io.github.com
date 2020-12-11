---
title: Automatically Clean Artifactory Artifacts
author: dfar

date: 2020-07-14T14:24:59+00:00
url: /automatically-clean-artifactory-artifacts/
categories:
  - Technology

---
If you&#8217;re running into issues with artifacts taking up too much space on your Artifactory server, this will set you up with a programmatic way to clean old artifacts as they are no longer required.

## Install ArtifactCleanup Plugin

SSH into the Artifactory server, go into the `$JFROG_HOME/etc/plugins` directory and run the following to download the [Artifact Cleanup plugin][1]:

<pre class="wp-block-code"><code>wget https://raw.githubusercontent.com/jfrog/artifactory-user-plugins/master/cleanup/artifactCleanup/artifactCleanup.groovy
wget https://raw.githubusercontent.com/jfrog/artifactory-user-plugins/master/cleanup/artifactCleanup/artifactCleanup.json</code></pre>

Make changes to the `artifactCleanup.json` file based on desired behavior.

To enable logging, add the following to `$ARTIFACTORY_HOME/etc/logback.xml`:

<pre class="wp-block-code"><code>&lt;logger name="artifactCleanup">
    &lt;level value="info"/>
&lt;/logger></code></pre>

Restart the server to install the plugin and get it set up for processing.

## Invoking Manually

Read more information on executing cleanup manually <a rel="noreferrer noopener" href="https://github.com/jfrog/artifactory-user-plugins/tree/master/cleanup/artifactCleanup#executing" target="_blank">on the GitHub page</a>. It&#8217;s worth running as a dry-run and making sure functionality works as intended before letting loose.

After running, you can check the logs `$ARTIFACTORY_HOME/logs/artifactory.log` to find the results of cleanup.

Remember that once deleted using the plugin, the artifacts will remain in the Trash Can if it is enabled on your instance.

 [1]: https://github.com/jfrog/artifactory-user-plugins/tree/master/cleanup/artifactCleanup