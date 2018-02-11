# Install Let's Encrypt

## Install the Certbot agent

Add the official repository:  
<pre>
sudo add-apt-repository ppa:certbot/certbot
</pre>

Update package lists:  
<pre>
sudo apt-get update
</pre>

Install the package:  
<pre>
sudo apt-get install certbot -y
</pre>

## Create snippets

As we don't want to use the same code in all our host configurations, we create 2 snippet files, that help to keep things simple.

Let's create the snippet directory:  
<pre>
sudo mkdir /etc/nginx/snippets
</pre>

Copy the snippets to nginx:  
<pre>
sudo cp ~/simple-node-server/starter-files/nginx/letsencrypt.conf /etc/nginx/snippets/letsencrypt.conf
sudo cp ~/simple-node-server/starter-files/nginx/ssl.conf /etc/nginx/snippets/ssl.conf
</pre>

## Create the folder for challenges

Let's encrypt needs a folder to temporarily store the challenges that are running. We'll create that now:  
<pre>
sudo mkdir -p /var/www/letsencrypt/.well-known/acme-challenge
</pre>

## Add first snippet to the host configuration

As we don't have a certificate, we'll include the first snippet in our host's configuration file. That's the boilerplate host in this example.

Open the config file:  
<pre>
sudo nano /etc/nginx/conf.d/boilerplate.<b>{your domain}</b>.conf
</pre>

Add the following line after "server_name":  
<pre>
include /etc/nginx/snippets/letsencrypt.conf;
</pre>

Restart the nginx server:  
<pre>
sudo systemctl restart nginx
</pre>

## Request a certificate

Now, that we've prepared the nginx configuration, we're able to request a new certificate for our demo subdomain:  
<pre>
sudo certbot certonly --webroot --agree-tos --no-eff-email --email <b>{your email}</b> -w /var/www/letsencrypt -d boilerplate.<b>{your domain}</b>
</pre>

The result should look like this:  
<pre>
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for boilerplate.your-domain.com
Using the webroot path /var/www/letsencrypt for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/boilerplate.your-domain.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/boilerplate.your-domain.com/privkey.pem
   Your cert will expire on 2018-05-11. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
</pre>

# Upgrade the host configuration

To use the SSL certificates for our demo domain, we'll upgrade the host configuration for nginx.

Open the config file:  
<pre>
sudo nano /etc/nginx/conf.d/boilerplate.<b>{your domain}</b>.conf
</pre>

Change the listen directives:  
<pre>
	listen 443 ssl http2;
	listen [::]:443 ssl http2;
</pre>

Remove the following snippet:  
<pre>
	include /etc/nginx/snippets/letsencrypt.conf;
</pre>

Add the SSL configuration right after "server_name":  
<pre>
	ssl_certificate /etc/letsencrypt/live/boilerplate.<b>{your-domain}</b>/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/boilerplate.<b>{your-domain}</b>/privkey.pem;
	ssl_trusted_certificate /etc/letsencrypt/live/boilerplate.<b>{your-domain}</b>/fullchain.pem;

	include /etc/nginx/snippets/ssl.conf;
</pre>

Add a new "server" block __before__ the existing one, to redirect non-https traffic:  
<pre>
server {
        listen 80;
        listen [::]:80;

        server_name boilerplate.<b>{your domain}</b>;

        include /etc/nginx/snippets/letsencrypt.conf;

        location / {
          return 301 https://$host$request_uri;
        }
}
</pre>

Check if the configuration works:  
<pre>
sudo service nginx configtest
</pre>

The result should look like this:  
<pre>
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
</pre>

If it doesn't, please check if you missed a semicolon or broke something during copy &amp; paste.

When everthing is fine, restart the nginx server:  
<pre>
sudo systemctl restart nginx
</pre>

You should be able to access the app using https now and all http requests should get redirected to https.

Open the http URL in your browser to test the behaviour and if the certificate is accepted:  
<pre>
http://boilerplate.<b>{your domain}</b>
</pre>

## Automate certificate renewal

Certbot can renew all certificates that expire within 30 days, so let's make a cron for it. To check if the current configuration works, you can launch a dry run:  
<pre>
sudo certbot renew --dry-run
</pre>

If this works without any issues let's create a new cronjob that renews the certificates automatically. We use a small shell script to restart all services that are working with our certificates, when they are changed.

Copy the script from the starter files and make it executable:  
<pre>
sudo cp ~/simple-node-server/starter-files/letsencrypt.sh /root/letsencrypt.sh
sudo chmod +x /root/letsencrypt.sh
</pre>

Open the crontab file for the root user:  
<pre>
sudo crontab -e
</pre>

Add the following line, to run the renewal process every day at 03:30:  
<pre>
30 3 * * * certbot renew --noninteractive --renew-hook /root/letsencrypt.sh
</pre>

---
__Next:__ [Cleanup](./cleanup.md)
