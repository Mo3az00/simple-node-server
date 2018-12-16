# Install Let's Encrypt

## Install the Certbot agent

Add the official repository:

<pre>
sudo add-apt-repository ppa:certbot/certbot -y
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

As we don't want to copy &amp; paste the same code in all our host configurations, we create 2 snippet files, that help us to keep things simple.

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

Open the config file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

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
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for boilerplate.dominik-hanke.de
Using the webroot path /var/www/letsencrypt for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/boilerplate.dominik-hanke.de/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/boilerplate.dominik-hanke.de/privkey.pem
   Your cert will expire on 2019-03-16. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
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
  ssl_certificate /etc/letsencrypt/live/boilerplate.<b>{your domain}</b>/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/boilerplate.<b>{your domain}</b>/privkey.pem;
  ssl_trusted_certificate /etc/letsencrypt/live/boilerplate.<b>{your domain}</b>/fullchain.pem;

  include /etc/nginx/snippets/ssl.conf;
</pre>

Add a new "server" block **before** the existing one, to redirect non-https traffic:

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

The resulting configuration should look similar to this:

<pre>
server {
        listen 80;
        listen [::]:80;

        server_name boilerplate.dominik-hanke.de;

        include /etc/nginx/snippets/letsencrypt.conf;

        location / {
          return 301 https://$host$request_uri;
        }
}

server {
        listen 443 ssl http2;
        listen [::]:443 ssl http2;

        server_name boilerplate.dominik-hanke.de;

        ssl_certificate /etc/letsencrypt/live/boilerplate.dominik-hanke.de/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/boilerplate.dominik-hanke.de/privkey.pem;
        ssl_trusted_certificate /etc/letsencrypt/live/boilerplate.dominik-hanke.de/fullchain.pem;

        include /etc/nginx/snippets/ssl.conf;

        access_log /var/log/nginx/boilerplate.dominik-hanke.de-access.log;
        error_log /var/log/nginx/boilerplate.dominik-hanke.de-error.log;

        location / {
                proxy_pass http://localhost:7000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

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

When everything is fine, restart the nginx server:

<pre>
sudo systemctl restart nginx
</pre>

You should be able to access the app using https now and all http requests should get redirected to https.

Open the http URL in your browser to test the behavior and if the certificate is accepted:

<pre>
http://boilerplate.<b>{your domain}</b>
</pre>

## Automate certificate renewal

Certbot can renew all certificates that expire within 30 days, so let's make a cron for it. To check if the current configuration works, you can launch a dry run:

<pre>
sudo certbot renew --dry-run
</pre>

If this works without any issues, we can create a new cronjob that renews the certificates automatically. We use a small shell script to restart all services that are working with our certificates, when they are changed.

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
# Start the Certbot to renew certificates, before they get outdated
30 3 * * * certbot renew --noninteractive --renew-hook /root/letsencrypt.sh
</pre>

---

**Next:** [Remove the demo project](./remove-the-demo-project.md)
