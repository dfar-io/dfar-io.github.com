---
title: Create a Java Web App with Spring Boot Starter, Gradle, and Azure Active Directory
author: dfar

date: 2019-03-13T18:36:50+00:00
excerpt: |
  
  <![CDATA[]]>
url: /create-a-java-web-app-with-spring-boot-starter-gradle-and-azure-active-directory/
categories:
  - Uncategorized

---
<!--[CDATA[
</p-->

Working on a project that would eventually use Azure Active Directory for authentication, I wanted to use [Microsoft&#8217;s guide][1]&nbsp;to get started with an app that could authenticate. However, the guide is written using Maven &#8211; and in our case, we are using Gradle for builds across the ecosystem.

I was able to mostly follow the guide provided, but ran into a gotcha &#8211; involving having to add a fixed dependency to get the system working. In addition, I added a secondary API endpoint to allow for testing both with and without group authentication.

To get started, you&#8217;ll need the following:

  * Azure Subscription
  * Java IDE (I use <a href="https://www.jetbrains.com/idea/download" target="_blank" rel="noreferrer noopener">IntelliJ IDEA Community</a>)

## Create an App With Spring Initializr

To get started, create an app at&nbsp;<https://start.spring.io/>&nbsp;with the following information:

  * **Project**: Gradle
  * **Project Metadata Artifact**:&nbsp;oauth2-demo-gradle
  * **Dependencies**: Web, Security, Azure Active Directory<figure class="wp-block-image">

<img src="http://40.76.37.251/wp-content/uploads/2019/03/image.png" alt="" class="wp-image-282" /> </figure> 

After clicking &#8216;Generate Project&#8217;, a ZIP file will be created. Extract this file to someone on your local machine, as you&#8217;ll be configuring it later.

## Set Up Azure Active Directory

After the app is created, the next step is setting up Azure Active Directory for authentication. Follow the&nbsp;<a href="https://docs.microsoft.com/en-us/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable#create-azure-active-directory-instance" target="_blank" rel="noreferrer noopener">Create&nbsp;Azure&nbsp;Active Directory instance</a> section, taking note of the App Registration information provided.

Once you get to the section involving configuration of the app, come back to this guide &#8211; you&#8217;ll make specific changes to make the testing experience both compatible with Gradle and a little more user-friendly.

## Configure and Compile the App

Once Azure Active Directory is configured, open your Java IDE and import the **build<g class="gr_ gr\_15 gr-alert gr\_gramm gr\_inline\_cards gr\_run\_anim Style replaceWithoutSep" id="15" data-gr-id="15">.<g class="gr_ gr\_4 gr-alert gr\_spell gr\_inline\_cards gr\_disable\_anim_appear ContextualSpelling" id="4" data-gr-id="4">gradle</g></g>** file as a project. After that&#8217;s done, there are a few files to configure/create.

Add the following dependencies into the **build.gradle** file _(notice the third dependency, if this one is not added, you&#8217;ll run into an exception when testing)_:

<pre class="wp-block-preformatted">implementation 'org.springframework.security:spring-security-oauth2-client'<br />implementation 'org.springframework.security:spring-security-oauth2-jose'<br />implementation 'com.nimbusds:oauth2-oidc-sdk:5.64.4'</pre>

Edit /src/main/resources/application.properties to look like the following:

<pre class="wp-block-preformatted"># Specifies your Active Directory ID:
azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

# Specifies your App Registration's Application ID:
spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

# Specifies your App Registration's secret key:
spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

# Specifies the list of Active Directory groups to use for authorization:
azure.activedirectory.active-directory-groups=Users</pre>

Create a file called **/controller/HelloController.java** and add the following:

<pre class="wp-block-preformatted">package com.example.oauth2demogradle.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.access.prepost.PreAuthorize;

@RestController
public class HelloController {
    @Autowired
    @RequestMapping("/version")
    public String helloWorld() {
        return "Hello World! (no auth required)";
    }

    @Autowired
    @RequestMapping("/secure")
    public String helloWorldSecure() {
        return "Hello World! (authenticated in Azure AD)";
    }

    @Autowired
    @PreAuthorize("hasRole('bams-devops')")
    @RequestMapping("/secureByGroup")
    public String helloWorldByGroup() {
        return "Hello World! (pre-authorized via AD group)";
    }
}</pre>

Create another file called security/WebSecurityConfig.java and add the following:

<pre class="wp-block-preformatted">package com.example.oauth2demogradle.security;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
import org.springframework.security.oauth2.core.oidc.user.OidcUser;

@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private OAuth2UserService&lt;OidcUserRequest, OidcUser> oidcUserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/version").permitAll()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
    }
}</pre>

## Build and Verify App

Now that the configuration is complete, <g class="gr_ gr\_4 gr-alert gr\_gramm gr\_inline\_cards gr\_run\_anim Grammar only-ins replaceWithoutSep" id="4" data-gr-id="4">next</g> step is building and testing. Run the following Gradle commands _(easy way to do this is through the IDE)_:

<pre class="wp-block-preformatted">clean<br />build<br />bootRun<br /></pre>

You should end up with a log like the following:<figure class="wp-block-image">

<img src="http://40.76.37.251/wp-content/uploads/2019/03/image-1.png" alt="" class="wp-image-283" /> </figure> 

Once this is confirmed, access <a rel="noreferrer noopener" aria-label="http://localhost:8080/version (opens in a new tab)" href="http://localhost:8080/version" target="_blank">http://localhost:8080/version</a>. You should be able to access **without** logging into Azure.

Next, check <http://localhost:8080/secure>. You should receive a request to log in to Azure services. Log in as the user created in the step above, and you should be able to get a Hello World message.

Finally, confirm that the /secureByGroup endpoint is also working. This endpoint will verify the logged in user is part of a verified group.

This code can be found at 

Reference:&nbsp;<https://docs.microsoft.com/en-us/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable>

]]>

 [1]: https://docs.microsoft.com/en-us/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable