# Install a system service

We will install a system service for our demo application and this should be the way you run all your live applications that you add to your server.

Our solution is not relying on third-party tools like pm2, so that we can use it without any additional packages and dependencies. One of the things many people don't know is that the Linux init system can restart crashed processes and much more, using unit files for configuration.

Let's start by creating a unit file for our application.

## Create a unit file

Copy the unit file from the starter files to systemd services:

<pre>
sudo cp ~/simple-node-server/starter-files/boilerplate.service /etc/systemd/system/boilerplate.<b>{your domain}</b>.service
</pre>

We have 5 settings that are interesting to change for most applications:

| Setting          | Description                                           |
| :--------------- | :---------------------------------------------------- |
| Description      | The description of the service you want to add        |
| User             | The user used to run the startup script               |
| Environment      | Environment variables that your application can read  |
| WorkingDirectory | The directory in which the application code is stored |
| ExecStart        | The command to run to start the application           |

Open the new unit file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a> and update the working directory:

<pre>
sudo nano /etc/systemd/system/boilerplate.<b>{your domain}</b>.service
</pre>
<pre>
WorkingDirectory=/var/www/node/boilerplate.<b>{your domain}</b>
</pre>

## Test the new service

Let's try to run our new service:

<pre>
sudo systemctl start boilerplate.<b>{your domain}</b>.service
</pre>

Verify that it's running:

<pre>
sudo systemctl status boilerplate.<b>{your domain}</b>.service
</pre>

The output should look like this:

<pre>
boilerplate.<b>{your domain}</b>.service - Express + Mongo Boilerplate
   Loaded: loaded (/etc/systemd/system/boilerplate.<b>{your domain}</b>.service; disabled; vendor preset: enabled)
   Active: active (running) since Sun 2018-12-16 18:03:28 UTC; 33s ago
 Main PID: 21052 (node)
    Tasks: 11 (limit: 1152)
   CGroup: /system.slice/boilerplate.<b>{your domain}</b>.service
           └─21052 /usr/bin/node start.js

Dec 16 18:03:28 web2 systemd[1]: Started Express + Mongo Boilerplate.
Dec 16 18:03:29 web2 node[21052]: Express running → PORT 7000
</pre>

When everything worked, we can add the service to the system boot so that it's started automatically every time the server is booting.

<pre>
sudo systemctl enable boilerplate.<b>{your domain}</b>.service
</pre>

---

**Next:** [Install Let's Encrypt](./install-lets-encrypt.md)
