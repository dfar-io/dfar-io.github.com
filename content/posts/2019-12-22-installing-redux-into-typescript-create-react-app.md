---
title: Installing Redux Into Typescript Create React App
author: dfar

date: 2019-12-22T22:49:04+00:00
url: /installing-redux-into-typescript-create-react-app/
categories:
  - Technology

---
To install Redux into an app created with Create React App that&#8217;s using Typescript, run the following to install:

<pre class="wp-block-code"><code>npm install --save redux react-redux @types/react-redux</code></pre>

Optionally, you can also install the following to allow for both:

  * Warning in case of state mutation.
  * Connecting to the app using <a rel="noreferrer noopener" aria-label="Redux DevTools (opens in a new tab)" href="https://github.com/reduxjs/redux-devtools" target="_blank">Redux DevTools</a>.

<pre class="wp-block-code"><code>npm i --save-dev redux-immutable-state-invariant
npm install --save redux-devtools-extension</code></pre>