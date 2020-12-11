---
title: Setting Up a Root Domain with Azure DNS
author: dfar

date: 2019-08-30T20:05:36+00:00
url: /setting-up-a-root-domain-with-azure-dns/
categories:
  - Uncategorized

---
When setting up Azure CDN, you may want to use a naked domain (yourdomain.com) to access. It&#8217;s a little tricky, so here&#8217;s how I set it up:

## Creation and Verification

Create the CDN profile and endpoint in Azure.

Next set up a custom domain:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-5.png" alt="" class="wp-image-588" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-5.png 504w, https://40.76.37.251/wp-content/uploads/2019/08/image-5-280x300.png 280w" sizes="(max-width: 504px) 85vw, 504px" /> </figure> 

To do this, you&#8217;ll need to modify the value highlighted as a CNAME record for your domain&#8217;s DNS. It will look like this:

  * Host: cdnverify.<yourdomain> 
  * Value: cdnverify.<endpoint>.azureedge.net 

## Setting up SSL and HTTPS Redirect

With the domain working, you&#8217;ll notice that you only have an insecure connection in place, so let&#8217;s set that up.

One of the disadvantages of using a root domain is that you cannot use the Azure CDN self-generated certificates. This means you&#8217;ll have to bring your own certificate in. There are three options immediately available:

  1. Purchase a certificiate from Azure (easy and reliable but starts at ~$70)
  2. Purchase a certificate from a reputable CA (Such as from Namecheap, can purchase a cert as low as ~$8 a year, although these certs are not as secure).
  3. Use the manual process at Let&#8217;s Encrypt to generate a certificate (free, but will need to be renewed regularly).

### Obtaining a Let&#8217;s Encrypt Certificate Using Windows

To use the manual process, you&#8217;ll need to start with the following:

Install WSL (Ubuntu 18.04) onto your computer, and open a CLI.

Install `certbot` with the following commands:

<pre class="wp-block-code"><code>sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot</code></pre>

And then run `certbot` in manual mode:

`sudo certbot certonly --manual --preferred-challenges dns-01`

You&#8217;ll need to create a TXT record with the data provided from `certbot`. After creating the TXT record, use [https://dnschecker.org][1] to verify the TXT record has been resolved before progressing (may take 5-10 minutes).

Once you&#8217;ve verified (and deleted the TXT record generated), you&#8217;ll have a certificate generated with both a certificate and private key. 

Next, you&#8217;ll add these to an Azure Key Vault to finish setting up HTTPS. 

## Adding Certificate to Key Vault, Configuring Certificate

To add the certificate to the keyvault, you need to first convert it to PFX (using WSL):

`openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -out cert.pfx -passout pass:password`

Now back to using Azure CLI, import the certificate:

`az keyvault certificate import --vault-name VAULT_NAME -n cert -f cert.pfx --password password`

Once the certificate is added, set up Custom HTTPS for the endpoint:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-6-995x1024.png" alt="" class="wp-image-594" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-6-995x1024.png 995w, https://40.76.37.251/wp-content/uploads/2019/08/image-6-291x300.png 291w, https://40.76.37.251/wp-content/uploads/2019/08/image-6-768x790.png 768w, https://40.76.37.251/wp-content/uploads/2019/08/image-6.png 1062w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /> </figure> <figure class="wp-block-image"><img src="https://dfar.io/wp-content/uploads/2019/08/image-8-1024x174.png" alt="" class="wp-image-600" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-8-1024x174.png 1024w, https://40.76.37.251/wp-content/uploads/2019/08/image-8-300x51.png 300w, https://40.76.37.251/wp-content/uploads/2019/08/image-8-768x130.png 768w, https://40.76.37.251/wp-content/uploads/2019/08/image-8.png 1061w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /></figure> 

Final steps are setting up a service principal for access with the command provided above. (Make sure to use `Connect-AzAccount`). Once this is done, you&#8217;ll need to allow some time (around 6-8 hours) to pass for allowing certificate import and CDN provisioning.

## Verification

To make sure everything is in place, first check to ensure the status shows Custom HTTPS being enabled:<figure class="wp-block-image">

<img src="https://dfar.io/wp-content/uploads/2019/08/image-7.png" alt="" class="wp-image-598" srcset="https://40.76.37.251/wp-content/uploads/2019/08/image-7.png 717w, https://40.76.37.251/wp-content/uploads/2019/08/image-7-300x171.png 300w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 984px) 61vw, (max-width: 1362px) 45vw, 600px" /> </figure> 

Afterwards, try accessing your site using HTTPS to confirm everything working.

## Redirecting all non-HTTP/root Traffic

The last step to getting this working is setting anything not going to `https://<yourdomain>.com` to the correct place. This can be done in the Premium CDN with Verizon plan by changing the traffic rules in the Custom rules engine:<figure class="wp-block-image">

<a href="https://dfar.io/wp-content/uploads/2019/09/image-2.png" target="_blank" rel="noreferrer noopener"><img src="https://dfar.io/wp-content/uploads/2019/09/image-2-1024x94.png" alt="" class="wp-image-606" srcset="https://40.76.37.251/wp-content/uploads/2019/09/image-2-1024x94.png 1024w, https://40.76.37.251/wp-content/uploads/2019/09/image-2-300x28.png 300w, https://40.76.37.251/wp-content/uploads/2019/09/image-2-768x71.png 768w" sizes="(max-width: 709px) 85vw, (max-width: 909px) 67vw, (max-width: 1362px) 62vw, 840px" /></a></figure> 

This will take some time to propagate, you&#8217;ll know it&#8217;s complete when you see &#8220;Active XML&#8221; next to the rule.

Once this is done, you can validate by trying to access the site using HTTP, and seeing it redirect to HTTPS (make sure to use an Incognito tab if using Chrome).

 [1]: https://dnschecker.org/