# Install demo project

We will install a demo application using Node.js, Express and MongoDB to test our setup.

## Create a database user

We want to creat a user per project database, to limit the access.

Connect to the Mongo shell:  
```
mongo
```

Create the new user:  
```
use admin
db.auth('databaseManager', '{your db admin password}')
db.createUser(
  {
    user: "boilerplate",
    pwd: "{create a new password}",
    roles: [ { role: "readWrite", db: "boilerplate" } ]
  }
);
```

## Clone the repository and install dependencies

Let's create a web root folder and clone the repository. I suggest to use a folder /var/www/node/ to store node applications.

```
mkdir -p /var/www/node/boilerplate.{your-domain}
cd /var/www/node/boilerplate.{your-domain}
git clone https://github.com/noreading/express-mongoose-boilerplate.git .
npm install
```

## Configure the application

Create a local configuration file and edit it with nano:  
```
cp variables.env.sample variables.env
nano variables.env
```

Update the following config lines:  
```
NODE_ENV=production

PORT=7000

SESSION_SECRET={a unique string for the cookie encryption}
SESSION_KEY={a unique string for the session cookie}

DATABASE_USERNAME=boilerplate
DATABASE_PASSWORD={the user's password}
DATABASE_NAME=boilerplate
```

## Create the host configuration

Open the starter configuration:  
```
nano ~/simple-node-server/starter-files/nginx/boilerplate.conf
```

Update the following line and replace the placeholder:  
```
        server_name boilerplate.{your domain};
```

Copy the config file to the nginx folder:  
```
cp ~/simple-node-server/starter-files/nginx/boilerplate.conf /etc/nginx/conf.d/boilerplate.{your domain}.conf
```

Check if the configuration is working:  
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
sudo service nginx restart
```

## Test the setup

To test our setup, we will run the the demo application manually.

```
cd /var/www/node/boilerplate.{your domain}
node start.js
```

If everything works well and there are no errors in your configurations you should be able to access the application in your browser:

```
http://boilerplate.{your domain}
```

Now, that we've seen that everything works, we'll stop the application by pressing [Strg] + [c] in the terminal to cancel the Node.js process.


---
__Next:__ [Install Let's Encrypt](./install-lets-encrypt.md)