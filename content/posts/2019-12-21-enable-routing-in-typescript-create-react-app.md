---
title: Enable Routing in Typescript Create React App
author: dfar

date: 2019-12-21T22:48:58+00:00
url: /enable-routing-in-typescript-create-react-app/
categories:
  - Technology

---
When working with Create React App using the Typescript configuration, there&#8217;s a slight change to be made to set up the ability to perform routing.

Install the following into your project:

<pre class="wp-block-code"><code>npm i -S react-router-dom @types/react-router-dom</code></pre>

After that&#8217;s done, use the content in the <a rel="noreferrer noopener" aria-label="Example Page (opens in a new tab)" href="https://reacttraining.com/react-router/web/example/basic" target="_blank">Example Page</a> in your `App.tsx` file. You should be able to change between pages using the URL as reference.

Reference:

<https://create-react-app.dev/docs/adding-a-router/>