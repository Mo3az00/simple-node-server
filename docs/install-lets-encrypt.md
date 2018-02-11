# Install Let's Encrypt

## Install the Certbot agent

Add the official repository:  
```
sudo add-apt-repository ppa:certbot/certbot
```

Update package lists:  
```
sudo apt-get update
```

Install the package:  
```
sudo apt-get install certbot -y
```

## Create snippets

As we don't want to use the same code in all our host configurations, we create 2 snippet files, that help to keep things simple.

Let's create the snippet directory:  
```
sudo mkdir /etc/nginx/snippets
```

Copy the snippets to nginx:  
```
sudo cp ~/simple-node-server/starter-files/nginx/letsencrypt.conf /etc/nginx/snippets/letsencrypt.conf
sudo cp ~/simple-node-server/starter-files/nginx/ssl.conf /etc/nginx/snippets/ssl.conf
```

## Create the folder for challenges

Let's encrypt needs a folder to temporarily store the challenges that are running. We'll create that now:  
```
sudo mkdir -p /var/www/letsencrypt/.well-known/acme-challenge
```

## Add first snippet to the host configuration

As we don't have a certificate, we'll include the first snippet in our host's configuration file. That's the boilerplate host in this example.

Open the config file:  
```
sudo nano /etc/nginx/conf.d/boilerplate.{your domain}.conf
```

Add the following line after "server_name":  
```
include /etc/nginx/snippets/letsencrypt.conf;
```

Restart the nginx server:  
```
sudo systemctl restart nginx
```

## Request a certificate

Now, that we've prepared the nginx configuration, we're able to request a new certificate for our demo subdomain:  
```
sudo certbot certonly --webroot --agree-tos --no-eff-email --email {your email} -w /var/www/letsencrypt -d boilerplate.{your domain}
```

The result should look like this:  
```
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
```

# Upgrade the host configuration

To use the SSL certificates for our demo domain, we'll upgrade the host configuration for nginx.

Open the config file:  
```
sudo nano /etc/nginx/conf.d/boilerplate.{your domain}.conf
```

Change the listen directives:  
```
	listen 443 ssl http2;
	listen [::]:443 ssl http2;
```

Remove the following snippet:  
```
	include /etc/nginx/snippets/letsencrypt.conf;
```

Add the SSL configuration right after "server_name":  
```
	ssl_certificate /etc/letsencrypt/live/boilerplate.{your-domain}/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/boilerplate.{your-domain}/privkey.pem;
	ssl_trusted_certificate /etc/letsencrypt/live/boilerplate.{your-domain}/fullchain.pem;

	include /etc/nginx/snippets/ssl.conf;
```

Add a new "server" block __before__ the existing one, to redirect non-https traffic:  
```
server {
        listen 80;
        listen [::]:80;

        server_name boilerplate.{your domain};

        include /etc/nginx/snippets/letsencrypt.conf;
        return 301 https://$host$request_uri;
}
```

Check if the configuration works:  
```
sudo service nginx configtest
```

The result should look like this:  
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

If it doesn't, please check if you missed a semicolon or broke something during copy &amp; paste.

When everthing is fine, restart the nginx server:  
```
sudo systemctl restart nginx
```

You should be able to access the app using https now and all http requests should get redirected to https.

Open the http URL in your browser to test the behaviour and if the certificate is accepted:  
```
http://boilerplate.{your domain}
```

## Automate certificate renewal

Certbot can renew all certificates that expire within 30 days, so let's make a cron for it. To check if the current configuration works, you can launch a dry run:  
```
sudo certbot renew --dry-run
```

If this works without any issues let's create a new cronjob that renews the certificates automatically. We use a small shell script to restart all services that are working with our certificates, when they are changed.

Copy the script from the starter files and make it executable:  
```
sudo cp ~/simple-node-server/starter-files/letsencrypt.sh /root/letsencrypt.sh
sudo chmod +x /root/letsencrypt.sh
```

Open the crontab file for the root user:  
```
sudo crontab -e
```

Add the following line, to run the renewal process every day at 03:30:  
```
30 3 * * * certbot renew --noninteractive --renew-hook /root/letsencrypt.sh
```

---
__Next:__ [Cleanup](./cleanup.md)
