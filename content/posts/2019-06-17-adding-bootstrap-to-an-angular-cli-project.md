---
title: Adding Bootstrap To An Angular CLI Project
author: dfar

date: 2019-06-17T14:17:19+00:00
url: /adding-bootstrap-to-an-angular-cli-project/
categories:
  - Uncategorized

---
## Pre-Reqs

Before setting this up, you&#8217;ll need to have an Angular project to add Bootstrap to. If you&#8217;re starting fresh, you can create one easily with `ng new <name>`.

## Procedure

First, install bootstrap from npm:

<pre class="wp-block-code"><code>npm install bootstrap</code></pre>

Then add the following to the top of your `styles.css` file:

<pre class="wp-block-code"><code>@import '~bootstrap/dist/css/bootstrap.min.css';</code></pre>

That&#8217;s it! Bootstrap is now enabled for your Angular CLI application.