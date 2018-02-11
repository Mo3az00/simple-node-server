# Install nginx

## Add the official repository

Copy the sourcelist file from the starter files for apt:  
```
curl -O https://nginx.org/keys/nginx_signing.key && sudo apt-key add ./nginx_signing.key
rm nginx_signing.key
sudo cp ~/simple-node-server/starter-files/nginx/nginx-org.list /etc/apt/sources.list.d/
```

## Install the package

```
sudo apt-get update
sudo apt-get install nginx -y
```

## Upgrade global configuration

Open the nginx config file:  
```
sudo nano /etc/nginx/nginx.conf
```

Remove the hashes before these 2 lines:
```
    #tcp_nopush     on;
    
    #gzip  on;
```

Add the following lines inside the "http" block:  
```
    gzip_disable "msie6";
    tcp_nodelay on;
    server_names_hash_bucket_size 128;
```

## Create our web project directory

We will store our web projects in the common folder /var/www/, that we might have to create first (depending on the used system image).

```
sudo mkdir /var/www
sudo chown {username}:{username} /var/www
```

## Create the default directory and files

Copy the ready index.html from the starter files to a new directory, that will be served by the webserver for any domain you don't want to be accesible.

```
mkdir /var/www/default
cp ~/simple-node-server/starter-files/index.html /var/www/default/
```

## Overwrite the default host configuration

Overwrite the nginx package default config with the starter file:  
```
sudo cp ~/simple-node-server/starter-files/nginx/default.conf /etc/nginx/conf.d/default.conf
```

Open the new config:  
```
sudo nano /etc/nginx/conf.d/default.conf
```

Update the following lines and replace placeholders:  
```
    server_name {your hostname};
```


---
__Next:__ [Install demo project](./install-demo-project.md)
