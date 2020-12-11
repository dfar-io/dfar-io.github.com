---
title: Adding Font Awesome to an Angular CLI Project
author: dfar

date: 2019-06-20T18:51:14+00:00
url: /adding-font-awesome-to-an-angular-cli-project/
categories:
  - Uncategorized

---
To add [Font Awesome][1] to your Angular CLI project, do the following:

Install Font Awesome:

<pre class="wp-block-code"><code>npm i @fortawesome/fontawesome-free</code></pre>

Then add the following to your `styles.css`:

<pre class="wp-block-code"><code>@import '~@fortawesome/fontawesome-free/css/all.min.css';</code></pre>

Once that&#8217;s done, use the following to add to your project:

<pre class="wp-block-code"><code>&lt;i class="far fa-copy">&lt;/i></code></pre>

 [1]: https://fontawesome.com