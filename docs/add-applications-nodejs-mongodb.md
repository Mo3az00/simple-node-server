# Add an application: Node.js &amp; MongoDB

## Create a SSH key for the application

The following commands create a new SSH key. We use the name of the Github repository in the name of the key file, so that it is easier to remember which key file was created for which Github project.

This will, of course, work in a similar way for Gitlab or Bitbucket.

<pre>
cd ~/.ssh
ssh-keygen -t rsa -b 4096 -f "github_{repository name}" -q -N "" -C "{username}@{application subdomain}{your domain}"
</pre>

Example:

<pre>
ssh-keygen -t rsa -b 4096 -f github_react-quiz -q -N "" -C "dominik@quiz.dominik-hanke.de"
</pre>

## Add the new key as Deploy key to the Github repository

We will add the generated key as "Deploy key" to github, with read-only access, to reduce unnecessary security risks. Go to your github project page, choose the tab "Settings" and click on "Deploy keys" in the sidebar on the left.

<img src="./images/github-deploy-key-list.png" alt="Github deploy key page" width="650">

Click on the button "Add deploy key" on the top-right and paste your deployment key into the form. To read the key and copy it, you can use the `cat` command.

<pre>
cat ~/.ssh/github_{repository name}.pub
</pre>

The deploy key form:

<img src="./images/github-deploy-key-dialog.png" alt="Photo: Github deploy key dialog for adding" width="650">

The new key will be shown as never used one in the list.

<img src="./images/github-deploy-key-list-updated.png" alt="Photo: Updated Github deploy key list" width="650">

## Create / update SSH config

If you don't have an SSH config file yet, we need to create that, as it is used to handle multiple SSH keys for different Github repositories. Create the config file with the following command:

<pre>
touch ~/.ssh/config && chmod 600 ~/.ssh/config
</pre>

Open the config file using nano:

<pre>
nano ~/.ssh/config
</pre>

Add a new configuration block for the application:

<pre>
# {application subdomain}{your domain}
Host {repository name} github.com
Hostname github.com
IdentityFile /home/{username}/.ssh/github_{repository name}
</pre>

Example:

<pre>
# quiz.dominik-hanke.de
Host react-quiz github.com
Hostname github.com
IdentityFile /home/dominik/.ssh/github_react-quiz
</pre>

## Clone the repository on your server

Let's clone the repository into a new directory named by the domain of the application. We use the SSH config to connect to the repository using the matching SSH key.

<pre>
git clone git@{repository name}:{github username}/{repository-name}.git /var/www/html/{application subdomain}{your domain}
</pre>

Example:

<pre>
git clone git@react-quiz:noreading/react-quiz.git /var/www/html/quiz.dominik-hanke.de
</pre>

## Install needed packages &amp; run build commands

Run the package installation:

<pre>
cd /var/www/node/<b>{application domain}</b>
npm install
</pre>

If your application has additional build / setup scripts, run them now. And if you have to set environment variables, this is again the right time to do that.

## Add a MongoDB User, if needed

If your application needs a MongoDB database, create a new user used for the connection.

Connect to the Mongo shell:

<pre>
sudo mongo
</pre>

Create the new user:

<pre>
use admin
db.auth('databaseManager', '<b>{your db admin password}</b>')

use <b>{app database name}</b>
db.createUser(
  {
    user: "<b>{app database username}</b>",
    pwd: "<b>{app database password}</b>",
    roles: [ { role: "readWrite", db: "<b>{app database name}</b>" } ]
  }
);
</pre>

## Add a new nginx host

Copy the template from the starter files:

<pre>
sudo cp ~/simple-node-server/starter-files/nginx/application-nodejs-mongodb.conf /etc/nginx/conf.d/<b>{application domain}</b>.conf
</pre>

Example:

<pre>
sudo cp ~/simple-node-server/starter-files/nginx/application-nodejs-mongodb.conf /etc/nginx/conf.d/quiz.dominik-hanke.de.conf
</pre>

Open the new configuration with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/nginx/conf.d/<b>{application domain}</b>.conf
</pre>

Update the config file and replace **all** the placeholders for the application domain, but **do not** remove the #hashes in front of the 3 SSL lines for now.

You also **need to** replace the port number with the one you want to use for your application. Each application needs to run on a **separate** port.

<pre>
proxy_pass http://localhost:{application port};
</pre>

Check if the configuration is working:

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
sudo service nginx restart
</pre>

## Generate a Let's Encrypt certificate

Now, that we've prepared the nginx configuration, we're able to request a new certificate for our application domain:

<pre>
sudo certbot certonly --webroot --agree-tos --no-eff-email --email <b>{your email}</b> -w /var/www/letsencrypt -d <b>{application domain}</b>
</pre>

The result should look like this:

<pre>
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for quiz.dominik-hanke.de
Using the webroot path /var/www/letsencrypt for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/quiz.dominik-hanke.de/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/quiz.dominik-hanke.de/privkey.pem
   Your cert will expire on 2019-03-16. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
</pre>

## Activate the certificate

Open the nginx host configuration with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/nginx/conf.d/<b>{application domain}</b>.conf
</pre>

Update the following lines and remove the #hashes in front of them:

<pre>
server {
    ...
    ssl_certificate /etc/letsencrypt/live/<b>{application domain}</b>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<b>{application domain}</b>/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/<b>{application domain}</b>/fullchain.pem;
    ...
}
</pre>

Restart the nginx server:

<pre>
sudo service nginx restart
</pre>

## Test the setup

To test our setup, we will run the the application manually.

<pre>
cd /var/www/node/<b>{application domain}</b>
node <b>{application starter file}</b>
</pre>

Example:

<pre>
cd /var/www/node/quiz.dominik-hanke.de
node app.js
</pre>

If everything works well and there are no errors in your configurations you should be able to access the application in your browser:

<pre>
https://<b>{application domain}</b>
http://<b>{application domain}</b>
</pre>

## Create a unit file

Copy the unit file from the starter files to systemd services:

<pre>
sudo cp ~/simple-node-server/starter-files/application.service /etc/systemd/system/<b>{application domain}</b>.service
</pre>

Example:

<pre>
sudo cp ~/simple-node-server/starter-files/application.service /etc/systemd/system/quiz.dominik-hanke.de.service
</pre>

We have 5 settings that are interesting to change for most applications:

| Setting          | Description                                           |
| :--------------- | :---------------------------------------------------- |
| Description      | The description of the service you want to add        |
| User             | The user used to run the startup script               |
| Environment      | Environment variables that your application can read  |
| WorkingDirectory | The directory in which the application code is stored |
| ExecStart        | The command to run to start the application           |

Open the new unit file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a> and replace the placeholders:

<pre>
sudo nano /etc/systemd/system/<b>{applicaton domain}</b>.service
</pre>
<pre>
Description=<b>{very short application description}</b>
...
WorkingDirectory=/var/www/node/<b>{application domain}</b>
ExecStart=/usr/bin/node <b>{application start script}</b>
Restart=always
RestartSec=500ms
StartLimitInterval=0

[Install]
WantedBy=multi-user.target
</pre>

## Test the new service

Let's try to run our new service:

<pre>
sudo systemctl start <b>{application domain}</b>.service
</pre>

Verify that it's running:

<pre>
sudo systemctl status <b>{application domain}</b>.service
</pre>

## Run the service on boot

When everything worked, we can add the service to the system boot so that it's started automatically every time the server is booting.

<pre>
sudo systemctl enable <b>{application domain}</b>.service
</pre>

---

**Next:** [Recurrent management tasks](./recurrent-management-tasks.md)
