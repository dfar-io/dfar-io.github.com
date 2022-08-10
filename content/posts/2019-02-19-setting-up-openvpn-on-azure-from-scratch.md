---
title: Setting up OpenVPN on Azure From Scratch
author: dfar

date: 2019-02-19T14:26:37+00:00
excerpt: |
  
  <![CDATA[]]>
url: /setting-up-openvpn-on-azure-from-scratch/
categories:
  - Technology

---
<!--[CDATA[
</p-->

Why do this? One of the major benefits being able to use the internet with a specified IP address. If you&#8217;re going to be working systems that whitelist specific IP addresses, you can use this solution to allow for access regardless of both machine and location.

This guide assumes that you:

  * Have an Azure subscription in place.
  * Have a means of SSHing into a virtual machine, such as [OpenSSH][1].

## Setting up OpenVPN Server

Create a virtual machine in Azure with the following specs:

  * Image: Ubuntu Server 18.04 LTS (or later)
  * Size: B1s
  * NSG Ports: 22, 80, 443



Once the virtual machine is created, create an inbound rule that allows access to port 943.

Once that&#8217;s done, SSH into the machine and install OpenVPN Access Server:

```
sudo su

apt update && apt -y install ca-certificates wget net-tools gnupg

wget -qO - https://as-repository.openvpn.net/as-repo-public.gpg | sudo apt-key add -

echo "deb http://as-repository.openvpn.net/as/debian bionic main" > /etc/apt/sources.list.d/openvpn-as-repo.list

apt update && apt -y install openvpn-as

exit
```

After OpenVPN is installed, set up an admin password:

`passwd openvpn`

After creating a password, log into OpenVPN with the above credentials at `https://YOUR_SERVER_ID/admin`.

## Enabling HTTPS with Let&#8217;s Encrypt

To set up a certificate for OpenVPN using Let&#8217;s Encrypt, first set up a domain to use with OpenVPN. This can either be:

  1. A domain created from a DNS name label in the Azure Public IP.
  2. A separate domain pointing to the DNS name label above with A and/or CNAME records.

install Certbot onto the OpenVPN server:

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update && sudo apt-get install -y certbot
```

Then run the following commands to stop OpenVPN, apply cert, and start OpenVPN again :

```
sudo service openvpnas stop

sudo certbot certonly  --standalone --non-interactive --agree-tos --email YOUR_EMAIL --domains YOUR_DOMAIN --pre-hook 'sudo service openvpnas stop' --post-hook 'sudo service openvpnas start'

sudo ln -s -f /etc/letsencrypt/live/YOUR_DOMAIN/cert.pem /usr/local/openvpn_as/etc/web-ssl/server.crt
sudo ln -s -f /etc/letsencrypt/live/YOUR_DOMAIN/privkey.pem /usr/local/openvpn_as/etc/web-ssl/server.key

sudo service openvpnas start
```

This command will also handle automatically renewing the cert every three months _(via pre-hook and post-hook)_, which **will shut down the VPN for a few seconds while renewing, while removing everyone&#8217;s connections**. If this is an issue, you can set up a reverse proxy with Apache or NGINX and apply the cert at the reverse proxy level, keeping users connected during renewal.

Finally, verify the URL above is secured.

### HTTP -> HTTPS Redirect

The last step is setting up HTTP to redirect to HTTPS, which can be done with a Python script. Create the following file at `/usr/local/openvpn_as/port80redirect.py`:

```
import SimpleHTTPServer
import SocketServer
class myHandler(SimpleHTTPServer.SimpleHTTPRequestHandler):
  def do_GET(self):
    print "Request received, sending redirect..."
    self.send_response(301)
    self.send_header('Location', 'https://YOUR_DOMAIN.com' + self.path)
    self.end_headers()
PORT = 80
handler = SocketServer.TCPServer(("", PORT), myHandler)
print "serving at port 80"
handler.serve_forever()
```

Now set up the script to run at boot:

`sudo crontab -e`

Add this line to the bottom:

`@reboot /usr/bin/screen -dmS port80redirect /usr/bin/python /usr/local/openvpn_as/port80redirect.py`

Reboot the server, and verify that accessing via HTTP redirects to HTTPS.

## References

Installing OpenVPN on Ubuntu: <https://openvpn.net/vpn-software-packages/ubuntu/>

Setting up Let&#8217;s Encrypt on OpenVPN AS: <https://loige.co/using-lets-encrypt-and-certbot-to-automate-the-creation-of-certificates-for-openvpn/>

Redirect HTTP -> HTTPS in OpenVPN: <https://openvpn.net/vpn-server-resources/how-to-redirect-http-to-https/>

Clearing configuration DB for renewal: <https://serverfault.com/a/1021892>

 [1]: https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse
