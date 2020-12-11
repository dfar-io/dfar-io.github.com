---
title: Generating CSRs the Easy Way In Windows
author: dfar

date: 2019-10-28T15:48:24+00:00
url: /generating-csrs-the-easy-way-in-windows/
categories:
  - Uncategorized

---
First, download the <a rel="noreferrer noopener" aria-label="DigiCert Certificate Utility (opens in a new tab)" href="https://www.digicert.com/util/" target="_blank">DigiCert Certificate Utility</a>.

Afterwards, click on &#8220;Create CSR&#8221;, and fill out the appropriate information to generate the CSR.

## Accessing the Private Key for the CSR

When a CSR is generated, there is a private key that associates with the CSR (and eventual certificate). You can access this in `certmgr.exe`:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-8-1024x563.png" alt="" class="wp-image-710" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-8-1024x563.png 1024w, https://40.76.37.251/wp-content/uploads/2019/10/image-8-300x165.png 300w, https://40.76.37.251/wp-content/uploads/2019/10/image-8-768x422.png 768w, https://40.76.37.251/wp-content/uploads/2019/10/image-8.png 1238w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> 

To get the private key, go to &#8216;All Tasks&#8217; -> &#8216;Export&#8217; and export the private key as needed. Windows will export it as a `.pfx` file, which you can convert using `openssl`:

<pre class="wp-block-code"><code>openssl pkcs12 -in exported_cert.pfx  -nocerts -out key.pem</code></pre>

When requested for the password and PEM passphrase, use the password provided in the export step above.