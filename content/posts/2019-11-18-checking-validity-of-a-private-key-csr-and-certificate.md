---
title: Checking Validity of a Private Key, CSR and Certificate
author: dfar

date: 2019-11-18T14:54:55+00:00
url: /checking-validity-of-a-private-key-csr-and-certificate/
categories:
  - Uncategorized

---
If you&#8217;re working with a certificate and need a means to check if the combination of the private key, CSR and certificate are all matching, you can run the following OpenSSL commands:

To check the private key:

`openssl rsa -modulus -noout -in YOUR_KEY.pem | openssl md5`

To check the CSR:

`openssl req -noout -modulus -in YOUR_CSR.csr | openssl md5`

To check the certificate:

`openssl x509 -modulus -noout -in CERTIFICATE.crt | openssl md5`

With each of these commands, you will have a value generated besides the `(stdin)` declaration, which needs to match across all of the different types to show compatibility.