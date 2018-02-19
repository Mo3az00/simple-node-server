# What you get with this tutorial

This tutorial will guide you to setup a secure server for running multiple [Node.js](https://nodejs.org/en/) applications with TLS encryption and a local [MongoDB](https://www.mongodb.com/) database. By using the [nginx](https://nginx.org/) webserver in front of Node.js we are able to run as many applications (with different domains) as we like.

Some parts of the tutorial, like setting up a local MongoDB server or [Let's Encrypt](https://letsencrypt.org/) for TLS encryption can be skipped, if you don't need them.

We'll also learn how to setup Node.js applications as system services that restart themselves on a crash and that run automatically on boot.

Software that will be installed:

- Node.js
- MongoDB
- Nginx
- Let's Encrypt
- Security packages:  
  fail2ban, logwatch, apticron

# Placeholders

Terminal commands and prepared files will have placeholders in them __that have to be replaced__ with values matching your setup. This could for example look like this:  
<pre>
ssh root@<b>{ip address}</b>
cd <b>{your application directory}</b>
</pre>

Your changed version should then look like this:  
<pre>
ssh root@192.168.100.50
cd /var/www/your-application/
</pre>

I suggest to create a text file where you store the details you use during this tutorial, so that you can copy &amp; paste them without always having to go back to your server management pages.

*new-server-config.txt*
```
[ General ]

  [ Host Configuration ]

    Domain:
    your-domain.com

    Hostname:
    node1.prod.your-domain.com

  [ SSH ]

    Username:    peter
    Password:    l0gM3!N?7&hc0m3L3tM3!nN0Wpl§$5%3
    Login:       ssh peter@node1.prod.your-domain.com

[ MongoDB ]

  [ Administrative User ]

    Database:  admin
    Username:  dbManager
    Password:  my53cur3L=g1nF0rd43mNm0ng0DB

  [ Boilerplate User ]

    Database:  boilerplate
    Username:  boilerplate
    Password:  my53cur3L=g1nF0rB01lerPl4T3
```

# Basic nano commands

We will use the [nano](https://www.nano-editor.org/) editor in this tutorial, but you're free to use any text editor you like in the terminal. If you're new to the console, here are some basic keyboard shortcuts you should know:

| Keys | Command | Description |
| :--- | :------ | :---------- |
|[Ctrl] + [a]|Position one|Move the cursor to the beginning of the current line.|
|[Ctrl] + [e]|End|Move the cursor to the end of the current line.|
|[Ctrl] + [v]|Page down|Moves the cursor down one page.|
|[Ctrl] + [y]|Page up|Moves the cursor up one page.|
|[Ctrl] + [k]|Cut a row|Cut a row and add it to memory.|
|[Ctrl] + [u]|Paste memory|Pastes the memory you've cut before.|
|[Ctrl] + [w]|Search|Opens a box at the bottom to search for strings.|
|[Ctrl] + [o] => [y] => [Enter]|Save file|Saves the changes and leaves editor open.|
|[Ctrl] + [x] => [y] => [Enter]|Close editor and save|Closes the editor and saves changes.|

# Table of Contents
1. [Register a free DigitalOcean account](./docs/register-a-free-digitalocean-account.md)
1. [Create your first droplet](./docs/create-your-first-droplet.md)
1. [Add a new user](./docs/add-a-new-user.md)
1. [Add security](./docs/add-security.md)
1. [Install Node.js](./docs/install-nodejs.md)
1. [Install MongoDB](./docs/install-mongodb.md)
1. [Install nginx](./docs/install-nginx.md)
1. [Install a demo project](./docs/install-a-demo-project.md)
1. [Install a system service](./docs/install-system-service.md)
1. [Install Let's Encrypt](./docs/install-lets-encrypt.md)
1. [Remove the demo project](./docs/reove-the-demo-project.md)
1. [Add your own application](./docs/add-your-own-application.md)
1. [Recurrent management tasks](./docs/recurrent-management-tasks.md)
1. [The End](./docs/the-end.md)
