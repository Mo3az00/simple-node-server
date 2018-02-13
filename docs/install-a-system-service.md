# Install a system service

We will install a system service for our demo application and this should be the way you run all your live applications that you add to your server.

Our solution is not relying on third-party tools like pm2, so that we can use it without any additional packages and dependencies. One of the thing many people don't know is that the Linux init system can restart crashed processes and much more, using unit files for configuration.

Let's start by creating a unit file for our application.

## Create a unit file

Copy the unit file from the starter files to systemd services:  
<pre>
sudo cp ~/simple-node-server/starter-files/boilerplate.service /etc/systemd/system/node-boilerplate.<b>{your domain}</b>.service
</pre>

We have 5 settings that are interesting to change for most applications:  

|Setting|Description|
|:------|:----------|
|Description|The description of the service you want to add|
|User|The user used to run the startup script|
|Environment|Environment variables that your application can read|
|WorkingDirectory|The directory in which the application code is stored|
|ExecStart|The command to run to start the application|

Open the new unit file and update the working directory:  
<pre>
sudo nano /etc/systemd/system/node-boilerplate.<b>{your domain}</b>.service
</pre>
<pre>
WorkingDirectory=/var/www/node/boilerplate.<b>{your domain}</b>
</pre>

## Test the new service

Let's try to run our new service:  
<pre>
sudo systemctl start node-boilerplate.<b>{your domain}</b>
</pre>

Verify that it's running:  
<pre>
sudo systemctl status node-boilerplate.<b>{your domain}</b>
</pre>

The output should look like this:  
<pre>
node-boilerplate.{your domain}.service.service - Express + Mongo Boilerplate
   Loaded: loaded (/etc/systemd/system/node-boilerplate.your-domain.com.service; disabled; vendor preset: enabled)
   Active: active (running) since Sun 2018-02-11 01:44:43 CET; 3min 48s ago
 Main PID: 29359 (node)
   CGroup: /system.slice/system-node\x2dnode\x2boilerplate.your\x2domain.com.slice/node-boilerplate.your-domain.com.service.service
           └─29359 /usr/bin/node start.js
</pre>

When everything worked, we can add the service to the system boot so that it's started automatically every time the server is booting.

<pre>
sudo systemctl enable node-boilerplate.<b>{your domain}</b>
</pre>

---
__Next:__ [Install Let's Encrypt](./install-lets-encrypt.md)