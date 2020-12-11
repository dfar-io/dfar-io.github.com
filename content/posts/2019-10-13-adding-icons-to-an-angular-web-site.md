---
title: Adding Icons to an Angular Web Site
author: dfar

date: 2019-10-13T16:43:10+00:00
url: /adding-icons-to-an-angular-web-site/
categories:
  - Uncategorized

---
Changing out the icons for an Angular website is just a few steps. This guide assumes you have an icon already in place, preferably in PNG format.

First, use a tool like <a href="https://realfavicongenerator.net/" target="_blank" rel="noreferrer noopener" aria-label="Real Favicon Generator (opens in a new tab)">Real Favicon Generator</a> to create the source files, which will include a `favicon.ico` file alongside a series of `apple-touch-icon*` files. Add these files to the `/src` directory.

After that, make the following change to your `index.html` file:

<pre class="wp-block-code"><code>&lt;head>
    ...
    &lt;link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
    &lt;link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
    &lt;link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
    &lt;link rel="manifest" href="/site.webmanifest">
    &lt;link rel="mask-icon" href="/safari-pinned-tab.svg" color="#5bbad5">
    &lt;meta name="msapplication-TileColor" content="#da532c">
    &lt;meta name="theme-color" content="#ffffff">
&lt;/head></code></pre>

And finally make the change to `angular.json` to ensure the files are delivered correctly on build:

<pre class="wp-block-code"><code>"assets": [
            ...
            "src/android-chrome-192x192.png",
            "src/android-chrome-512x512.png",
            "src/apple-touch-icon.png",
            "src/browserconfig.xml",
            "src/favicon-16x16.png",
            "src/favicon-32x32.png",
            "src/mstile-150x150.png",
            "src/safari-pinned-tab.svg",
            "src/site.webmanifest"
          ],</code></pre>

To test that everything worked successfully, make sure to run a build:

`ng build`

After this finishes, check the `/dist` folder to see the icon files added in the step above.