---
title: Suggested Jenkins Plugins
author: dfar

date: 2019-07-11T13:55:59+00:00
url: /suggested-jenkins-plugins/
categories:
  - Uncategorized

---
Here&#8217;s a list of Jenkins plugins I tend to use frequently.

### Global Slack Notification Plugin

Allows for sending messages via pipeline to Slack channels.

### Azure AD Plugin

Allows for authentication to Jenkins using Azure Active Directory.

### Checkmarx

If you are using Checkmarx to scan for vulnerabilities in your codebases, this plugin will allow for connecting to a Checkmarx server automatically to generate a report.

After installing, make sure to set up a server to allow for connection in Configure System -> Checkmarx:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-5-1024x312.png" alt="" class="wp-image-523" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-5-1024x312.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-5-300x92.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-5-768x234.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

If you need to add the Checkmarx step to the pipeline, this can be generated using the Pipeline Syntax feature.

### NodeJS

This allows for using `npm` steps in your project, great for running processes for JavaScript based projects.

After installing, make sure to set up a NodeJS installation in Global Tool Configuration:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-6-1024x294.png" alt="" class="wp-image-526" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-6-1024x294.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-6-300x86.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-6-768x220.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

## Build with Parameters Plugin

If you have a job that takes a series of parameters to run (such as a job that performs a deployment), this plugin will allow for programmatically filling in the parameters via a GET call.

Once installed, you can call any job with parameters like such:

https://server/job/$JOB/buildwithparam?PARAMS

## Windows Azure Storage Plugin

This plugin allows for programmatic interaction with different Azure storage accounts, allowing for both uploading and downloading.

After installing, you&#8217;ll need to set up credentials for use:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/07/image-8-1024x251.png" alt="" class="wp-image-546" srcset="https://40.76.37.251/wp-content/uploads/2019/07/image-8-1024x251.png 1024w, https://40.76.37.251/wp-content/uploads/2019/07/image-8-300x74.png 300w, https://40.76.37.251/wp-content/uploads/2019/07/image-8-768x188.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

To use in pipeline, the syntax will look like:

<pre class="wp-block-code"><code>azureUpload blobProperties: [
                        cacheControl: '',
                        contentEncoding: '',
                        contentLanguage: '',
                        contentType: '',
                        detectContentType: true],
                        containerName: 'ui-dist',
                        fileShareName: '',
                        filesPath: 'dist/**',
                        storageCredentialId: 'YOUR-SP',
                        storageType: 'blobstorage'</code></pre>

## Active Choices Plugin

This allows for more dynamic functionality with parameters (selecting one parameter allows a set of options for the other).

Setting this up in Pipeline takes some work, you&#8217;ll need to add using the following format:

<pre class="wp-block-code"><code>properties([
    parameters([
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the context from the Dropdown List', 
            filterLength: 1, 
            filterable: true, 
            name: 'context', 
            randomName: 'choice-parameter-5631314439613978', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        'return[\'Could not get Env\']'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        'return["uat","dev"]'
                ]
            ]
        ], 
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the Server from the Dropdown List', 
            filterLength: 1, 
            filterable: true, 
            name: 'service', 
            randomName: 'choice-parameter-5631314456178619', 
            referencedParameters: 'Env', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        'return[\'Could not get Environment from Env Param\']'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        ''' if (context.equals("uat")){
                                return["uat-1","uat-2"]
                            }
                            else if(context.equals("dev")){
                                return["dev-1","dev-2"]
                            }
                        '''
                ]
            ]
        ]
    ])
])</code></pre>