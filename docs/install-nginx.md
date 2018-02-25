# Install nginx

## Add the official repository

Copy the sourcelist file from the starter files for apt:  
<pre>
curl -O https://nginx.org/keys/nginx_signing.key &amp;&amp; sudo apt-key add ./nginx_signing.key
rm nginx_signing.key
sudo cp ~/simple-node-server/starter-files/nginx/nginx-org.list /etc/apt/sources.list.d/
</pre>

## Install the package

<pre>
sudo apt-get update &amp;&amp; sudo apt-get install nginx -y
</pre>

## Upgrade global configuration

Open the nginx config file:  
<pre>
sudo nano /etc/nginx/nginx.conf
</pre>

Remove the hashes before these 2 lines:
<pre>
    #tcp_nopush     on;
    #gzip  on;
</pre>

Add the following lines inside the "http" block:  
<pre>
    gzip_disable "msie6";
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript image/svg image/svg+xml;
    tcp_nodelay on;
    server_names_hash_bucket_size 128;
</pre>

## Create our web project directory

We will store our web projects in the common folder /var/www/, that we might have to create first (depending on the used system image).

<pre>
sudo mkdir /var/www
sudo chown <b>{username}</b>:<b>{username}</b> /var/www
</pre>

## Create the default directory and files

Copy the ready index.html from the starter files to a new directory, that will be served by the webserver for any domain you don't want to be accessible.

<pre>
mkdir /var/www/default
cp ~/simple-node-server/starter-files/index.html /var/www/default/
</pre>

## Overwrite the default host configuration

Overwrite the nginx package default config with the starter file:  
<pre>
sudo cp ~/simple-node-server/starter-files/nginx/default.conf /etc/nginx/conf.d/default.conf
</pre>

Open the new config:  
<pre>
sudo nano /etc/nginx/conf.d/default.conf
</pre>

Update the following lines and replace placeholders:  
<pre>
    server_name <b>{your hostname}</b>;
    
    access_log /var/log/nginx/<b>{your hostname}</b>.access.log;
    error_log /var/log/nginx/<b>{your hostname}</b>.error.log;
</pre>

---
__Next:__ [Install demo project](./install-a-demo-project.md)
