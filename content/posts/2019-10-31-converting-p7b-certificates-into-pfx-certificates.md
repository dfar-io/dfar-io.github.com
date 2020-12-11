---
title: Converting P7B Certificates into PFX Certificates
author: dfar

date: 2019-10-31T14:40:55+00:00
url: /converting-p7b-certificates-into-pfx-certificates/
categories:
  - Uncategorized

---
To convert a P7B certificate into a PFX certificate, you&#8217;ll need the following:

  * The `.p7b` certificate created after the CSR is generated.
  * The private key (likely `.pem` or `.key`) generated when generating the CSR.

First, doublr click the `.p7b` file and export out all of the certs that appear in Certificate Manager as Base64 encoded `.CER` files:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/10/image-9.png" alt="" class="wp-image-713" srcset="https://40.76.37.251/wp-content/uploads/2019/10/image-9.png 696w, https://40.76.37.251/wp-content/uploads/2019/10/image-9-300x249.png 300w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> </figure> 

Once this is done, you&#8217;ll be able to create the `.PFX` file with the following `openssl` command:

<pre class="wp-block-code"><code>openssl pkcs12 -export -in certificatename.cer -inkey privateKey.key -out certificatename.pfx -certfile cacert1.cer -certfile cacert2.cer </code></pre>