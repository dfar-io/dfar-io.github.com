---
title: 'Formatting Dates in  Create React App with MomentJS'
author: dfar

date: 2019-12-21T20:32:14+00:00
url: /adding-moment-js-to-create-react-app/
categories:
  - Technology

---
While working with React, I found I wanted a way to work with dates similiar to the way Angular uses the DatePipe to handle displaying dates correctly. The answer is in using the [momentjs][1] library.

To do so, add the package to your project:

<pre class="wp-block-code"><code>npm i --save moment</code></pre>

Then add the following code to test in one of your `.js` or `.tsx` files:

<pre class="wp-block-code"><code>import moment from 'moment';

...

{moment().format()}</code></pre>

You should see an ISO string being displayed. To check on ways you can use the library, check the docs on the page above.

 [1]: https://momentjs.com/