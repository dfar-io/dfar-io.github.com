---
title: Setting up Slack Notifications with Jenkins
author: dfar

date: 2019-09-21T16:00:27+00:00
url: /setting-up-slack-notifications-with-jenkins/
categories:
  - Uncategorized

---
Setting up Slack notifications for build results with Jenkins is pretty simple. You&#8217;ll need the following:

  * A Jenkins server that you have admin rights to.
  * <a rel="noreferrer noopener" aria-label="Slack Notification Plugin (opens in a new tab)" href="https://plugins.jenkins.io/slack" target="_blank">Slack Notification Plugin</a>
  * A Slack workspace, ideally with a channel dedicated to Jenkins alerts.

## Setup

To set up Slack, go to **<your-workspace-name>.slack.com/apps** and search for Jenkins CI:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-5-1024x601.png" alt="" class="wp-image-636" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-5-1024x601.png 1024w, https://40.76.37.251/wp-content/uploads/2019/09/image-5-300x176.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-5-768x451.png 768w, https://40.76.37.251/wp-content/uploads/2019/09/image-5.png 1483w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

Add the configuration and set the desired channel where alerts will appear.

After this, you&#8217;ll see some directions on setting up Slack on the Jenkins side.

When you&#8217;ve finished the setup, make sure to test the connection &#8211; you should see a message in Slack like the following:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/09/image-7.png" alt="" class="wp-image-638" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-7.png 867w, https://40.76.37.251/wp-content/uploads/2019/09/image-7-300x36.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-7-768x93.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Adding Jenkins Calls to Pipeline

Once the setup is complete, you can use the following examples to call Slack during the pipeline jobs:

<pre class="wp-block-code"><code>stages {
  stage('Build') {
    steps {
      echo 'build'
    }
  }
  stage('Deploy') {
    steps {
      echo 'Deploy'
      slackSend color: 'good', message: "deployment successful => https://yoursite.com"
    }
  }
}
...
post {
  failure {
    slackSend color: 'danger', message: "deployment failed (&lt;${env.BUILD_URL}|Open>)"
  }
  always {
    cleanWs()
  }
}</code></pre>

This example will call a successful message when all stages complete, and sends a failure message if the build as a whole fails.