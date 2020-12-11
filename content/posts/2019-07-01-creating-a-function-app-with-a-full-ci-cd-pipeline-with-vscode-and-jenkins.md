---
title: Creating a Function App With a Full CI/CD Pipeline with VSCode and Jenkins
author: dfar

date: 2019-07-01T14:55:01+00:00
url: /creating-a-function-app-with-a-full-ci-cd-pipeline-with-vscode-and-jenkins/
categories:
  - Uncategorized

---
Before starting this, you&#8217;ll need to have a few things on your machine:

  * [Azure Functions VSCode Extension][1]
  * Azure Functions Core Tools (`choco install azure-functions-core-tools`)
  * .NET Core Build Tools (`choco install visualstudio2017-workload-netcorebuildtools`)
  * An Azure subscription
  * A Function app created inside of the Azure subscription
  * A Jenkins server with the following set up:
      * Azure Function plugin installed
      * A service principal configured (use `az ad sp create-for-rbac -n "jenkins" --role contributor --scopes /subscriptions/{SubID}` and then add to Jenkins Credentials)

## Creating and Locally Running the Function App

Inside VSCode, create a function app project with the following:

  * Create a new folder for use
  * Language: C#
  * Template: HttpTrigger
  * Security: Anonymous

After creating, you&#8217;ll need to resolve a few dependencies, which VSCode should prompt for.

I ran into issues with getting the function app debugging locally, but I was able to run it without issue by running the following in PowerShell

<pre class="wp-block-code"><code>dotnet clean
dotnet build
func host start</code></pre>

Once you get it running, you can use the URL provided to test:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image.png" alt="" class="wp-image-467" /> </figure> 

### Locally Running a TimerTrigger Function

To use a TimerTrigger functions, there are just a few changes:

  * You will need to have a Storage Account available to allow for running locally.
  * When running, you can invoke the function by making a POST to `http://localhost:7071/admin/functions/{FUNCTION_NAME}` _(make sure to provide an empty JSON body in the payload)_

## Adding to Git and Deploying via Jenkins

The next step is checking in the example code to Git, so you have a place to get the codebase from for deployment.

After checking in the codebase, create a Multibranch Pipeline project in Jenkins.

Use the following Jenkinsfile as a reference:

<pre class="wp-block-code"><code>pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'dotnet clean'
        sh 'dotnet build'
      }
    }
    stage('Deploy to Function App') {
      when { branch 'master' }
      steps {
        azureFunctionAppPublish appName: "fa-poc-123",
          azureCredentialsId: 'jenkins-sp',
          resourceGroup: "fa-poc-ue-rg",
          sourceDirectory: '',
          targetDirectory: '',
          filePath: ''
      }
    }
  }
}</code></pre>

After that, check in your codebase and you should have a deployed Function App with the codebase provided. Verify it using the Function App URL provided.

 [1]: vscode:extension/ms-azuretools.vscode-azurefunctions