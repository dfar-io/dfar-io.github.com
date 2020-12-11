---
title: Deploying web.config with an Angular Project
author: dfar

date: 2019-06-17T18:35:57+00:00
url: /deploying-web-config-with-an-angular-project/
categories:
  - Uncategorized

---
When deploying an Angular project out to Azure, you&#8217;ll need to include a `web.config` file to allow for things such as the following:

  * Getting routing to work.
  * Serving static content.

First, create a `web.config` file in `src/`. Here&#8217;s an example of what it might look like:

<pre class="wp-block-code"><code>&lt;?xml version="1.0"?>
&lt;configuration>
    &lt;system.webServer>
        &lt;rewrite>
            &lt;rules>
                &lt;rule name="Angular Routing" stopProcessing="true">
                &lt;match url=".*" />
                &lt;conditions logicalGrouping="MatchAll">
                    &lt;add input="{REQUEST_FILENAME}" matchType="IsFile"
                         negate="true" />
                    &lt;add input="{REQUEST_FILENAME}" matchType="IsDirectory"
                         negate="true" />
                &lt;/conditions>
                &lt;action type="Rewrite" url="/" />
                &lt;/rule>
            &lt;/rules>
        &lt;/rewrite>
        &lt;staticContent>
            &lt;mimeMap fileExtension="woff" mimeType="application/font-woff" />
            &lt;mimeMap fileExtension="json" mimeType="application/json" />
        &lt;/staticContent>
    &lt;/system.webServer>
&lt;/configuration></code></pre>

After this is done, make a change to `angular.json` to bundle the `web.config` file in the build:

<pre class="wp-block-code"><code>...
"assets": [
    /src/favicon.ico",
    /src/assets",
    /src/web.config"
],
...</code></pre>

Now let&#8217;s verify by running `ng build --prod`:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/06/image-4.png" alt="" class="wp-image-442" /> </figure>