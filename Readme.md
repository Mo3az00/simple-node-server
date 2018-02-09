# WORK IN PROGRESS

Do not use this manual, until this comment is removed, as it's work in progress and incomplete.  
Feel free to come back later and give it a try. :-)

# 1. Register a free account for the Hetzner services (Robot, Cloud, etc.)

__Important:__  
You need to wait for an activation email and it can take some additional time until all features of the account are active.

https://accounts.hetzner.com/login?_locale=en

# 2. Create your first project

You should create a project with a useful name, to group all the servers you might rent in the future.

Examples:  
```DCI FbW V```  
```Private```  
```Friends```

# 3. Add your SSH keys

Go to the SSH Key Management page and add all of the SSH keys that you want to add to your servers. This is useful because you can add the keys that you need from home, office, etc. and you don't have to copy & paste them for every new server. This will be handy, even if you think "I will not have more than 1 server", as you might have some problems when you try new softare and you might want to redeploy the server or destro it and create a new one.

To get your "main" SSH key on Ubuntu, that you should have generated for Git already, run this command:  
```cat ~/.ssh/id_rsa.pub```

# 4. Create your first server

## Launch creation process

Go to the Server Management page and create your first server.  
The following selections will fit for your needs.

## Location

You can decide which location you like. Nuremberg is a bit closer to Berlin, but that shouldn't make a big difference. ;-)

## Image

Choose "Ubuntu 16.04" as this is a common image for servers and you're alreading using Ubuntu on your computers.

## Type

The smallest type for less than 3 € per month is good enough for multiple Node.js projects, so that there's no need to take a bigger one for your first steps.

## Additional features

You do not need to add user data right now. This is for more advanced server setups where you want to use some kind of auto-configuration based on the data you pass to the server when it's created.

## Name

I'm a fan of using a hostname that makes sense and groupse your servers by type.  
A good example for a node.js sever could be:  
```node1.prod.your-domain.com```

# 5. Login by SSH

```ssh root@node1.prod.your-doman.com```

# 6. Add new user and block root access

## Add new user + sudo

Add a new user for your machine and use your firstname to have something speaking, if you want to remember that later. ;-)

```useradd -m {your firstname}```

Give full sudo access to that user, without asking for the password:

```nano /etc/sudoers```

Add the following lines:  
```
# Allow user to execute any command without password prompt
{your firstname} ALL=(ALL) NOPASSWD:ALL
```

Save and close the file using [Ctrl] + [x] => [y] => [Enter].

## Add SSH key and login as new user

Change to the new user with the following command:  
```su - {your firstname}```

Create the directory ".ssh":  
```mkdir .ssh && chmod 700 -ssh```

Change into that directory:
```cd .ssh```

Create a new file "authorized_keys":  
```nano authorized_keys```

Paste your SSH keys (one per line) into this file.  
Save and close the file using [Ctrl] + [x] => [y] => [Enter].

Login as the new user:  
```ssh {your firstname} node1.prod.your-domain.com```

## Block root access

Open the editor for the SSH daemon:  
```sudo nano /etc/ssh/sshd_config```

Search ([Strg] + [w]) for the string "PermitRootLogin".  
Change the value from "yes" to "no":  
```PermitRootLogin yes```

Search for the string "PasswordAuthentication".  
Remove the hash and change the value from "yes" to "no":  
```PasswordAuthentication```

Save and close the file using [Ctrl] + [x] => [y] => [Enter].

Restart the SSH service:  
```sudo service ssh restart```

# 7. Security

Run the following command:  
```sudo apt-get install ufw fail2ban apticron unattended-upgrades```

## Configure firewall

Run the following commands to disallow all traffic that is not coming to a webserver or for the SSH connection:

```bash
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
```

## Install logwatch

```sudo apt-get install logwatch```

When you're prompted for the server type, please select "Internet" which should be the default and submit with [Enter]. The hostname should be prefilled, too, so that you can hit [Enter] a second time.

## Harden the network layer

Open the configuration file:  
```sudo nano /etc/sysctl.conf```

Uncomment the following lines:  
```bash
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1

net.ipv4.tcp_syncookies=1

net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0

net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0

net.ipv4.conf.all.log_martians = 1

```

# 8. Install Node.js and MongoDB

Run the following commands:
```
cd ~
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
chmod 755 nodesource_setup.sh
sudo ./nodesource_setup.sh
sudo apt-get install nodejs 
rm nodesource_setup.sh
```

Run the following command:  
```sudo apt-get install mongodb```

Create the main folder for your Node.js application:  
```bash
sudo mkdir /var/www/node
sudo chown -R {your firstname}:{your firstname} /var/www
```

We will install a boilerplate as first application, to showcase the needed steps.  
Create a folder for the project:  
```bash
mkdir /var/www/node/expressjs-bootstrap-boilerplate
cd /var/www/node/expressjs-bootstrap-boilerplate
```

Clone and install the boilerplate:  
```bash
git clone https://github.com/noreading/expressjs-bootstrap-boilerplate.git .    
npm install
cp variables.env.sample variables.env
```

# 9. Install and configure Nginx

## Installation

Run the following command:  
```sudo apt-get install nginx```

## Create default website

All requests, that do not match a configured domain, will land on a default page, that tells them they reached the end of the internet.

Create the folder:  
```mkdir /var/www/default```

Create the index file:  
```nano /var/www/index.html```

Paste the content of [this file](./index.html) into the editor.  
Save and close the file using [Ctrl] + [x] => [y] => [Enter].

Change to the nginx configuration folder:  
```cd /etc/nginx/sites-available/```

Edit the default config:  
```sudo nano default```

Remove all contents ([Strg] + [k] again and again until it's empty) and paste this content:  

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        server_name node1.prod.your-domain.com;

        location / {
                root /var/www/default;
                index index.html;
        }

        location ~ /.well-known {
                allow all;
        }
}
```

## Allow longer hostnames

Open the config file:  
```sudo nano /etc/nginx/nginx.conf```

Uncomment the following line and change the value from "64" to "128":  
```server_names_hash_bucket_size 128;```

## Configure boilerplate apllication

Add a new config file for our application:  
```sudo nano expressjs-bootstrap-boilerplate.your-domain.com```

Paste __and edit__ the following content:  
```
# reverse proxy
server {
        listen 80;
        listen [::]:80;
        
        # Server Name
        server_name expressjs-bootstrap-boilerplate.your-domain.com;

        # Logs
        access_log /var/log/nginx/expressjs-bootstrap-boilerplate.your-domain.com-access.log;
        error_log /var/log/nginx/expressjs-bootstrap-boilerplate.your-domain.com.de-error.log;

        location / {
                proxy_pass http://localhost:7777;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
}
```

Change to the enabled sites folder:  
```cd /etc/nginx/sites-enabled/```

Create a symlink for the new configuration:  
```sudo ln -s ../sites-available/expressjs-bootstrap-boilerplate.your-domain.com```

Check if the nginx config is still okay:  
```sudo service nginx configtest```

If there's an error: Ask your instructor!  
If it says "[ok]" everything is nice.

Restart the nginx server:  
```sudo service nginx restart```

Start the Node.js application:  
```
cd /var/www/node/expressjs-bootstrap-boilerplate
node ./app.js
```