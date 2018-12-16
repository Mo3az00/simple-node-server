# What you get with this tutorial

This tutorial will guide you to setup a secure server for running multiple [Node.js](https://nodejs.org/en/) applications with TLS encryption and a local [MongoDB](https://www.mongodb.com/) database. By using the [nginx](https://nginx.org/) webserver in front of Node.js we are able to run as many applications (with different domains) as we like.

We'll also learn how to setup Node.js applications as system services that restart themselves on a crash and that run automatically on boot.

It is important to know that this tutorial is based on using DigitalOcean to setup your DNS settings. This means that you don't need to know anything about DNS, if you use a domain you don't use somewhere else. But you should have a good knowledge about DNS if you want to setup a domain that you used for other hosting providers already, without using the DNS service of DigitalOcean.

Software that will be installed:

- Ubuntu 18.04 LTS
- Node.js
- MongoDB
- Nginx
- Let's Encrypt
- Security packages:  
  fail2ban, logwatch, apticron

# Your own domain

A requirement to be able to run your own server with websites and applications is to have at least one domain name registered.

If you don't have a domain yet, I suggest to start with an easy domain provider, that doesn't make things complicated: [gandi.net](https://www.gandi.net/en). The price for a .com domain is 15.47 € per year (pricing at the 15th of December 2018).

# Placeholders

Terminal commands and prepared files will have placeholders in them **that have to be replaced** with values matching your setup. This could for example look like this:

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

The things you need to have / create, before you can start to setup your server, are shown in the table below.

| Name                        | Description                                                                                                                       | Example                                    |
| :-------------------------- | :-------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------- |
| Domain                      | This is the domain you want to use for the first websites / applications you want to run on your server                           | my-domain.com                              |
| Hostname                    | This should be a unique subdomain of your main domain, that is only used to access the server                                     | node1.prod.my-domain.com                   |
| SSH Username                | The username that is used to authenticate yourself on the server, when you connect using the terminal                             | dominik                                    |
| SSH Password                | The password used to connect to your server using the DigitalOcean Terminal, if you somehow loose your SSH keys                   |
| MongoDB Administrative User | The database is always "admin" and you should choose a strong password as this is the database user that has access to everything | dbManager<br>my53cur3L=g1nF0rd43mNm0ng0DB  |
| MongoDB Boilerplate User    | This user is only created for testing purposes, to check if the setup works as expected                                           | boilerplate<br>my53cur3L=g1nF0rB01lerPl4T3 |

_new-server-config.txt_

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

| Keys                           | Command               | Description                                           |
| :----------------------------- | :-------------------- | :---------------------------------------------------- |
| [Ctrl] + [a]                   | Position one          | Move the cursor to the beginning of the current line. |
| [Ctrl] + [e]                   | End                   | Move the cursor to the end of the current line.       |
| [Ctrl] + [v]                   | Page down             | Moves the cursor down one page.                       |
| [Ctrl] + [y]                   | Page up               | Moves the cursor up one page.                         |
| [Ctrl] + [k]                   | Cut a row             | Cut a row and add it to memory.                       |
| [Ctrl] + [u]                   | Paste memory          | Pastes the memory you've cut before.                  |
| [Ctrl] + [w]                   | Search                | Opens a box at the bottom to search for strings.      |
| [Ctrl] + [o] => [y] => [Enter] | Save file             | Saves the changes and leaves editor open.             |
| [Ctrl] + [x] => [y] => [Enter] | Close editor and save | Closes the editor and saves changes.                  |

# Table of Contents

1. [Register a free DigitalOcean account](./docs/register-a-free-digitalocean-account.md)
1. [Create your first droplet](./docs/create-your-first-droplet.md)
1. [Add a new user](./docs/add-a-new-user.md)
1. [Add security](./docs/add-security.md)
1. [Install Node.js](./docs/install-nodejs.md)
1. [Install MongoDB](./docs/install-mongodb.md)
1. [Install nginx](./docs/install-nginx.md)
1. [Install a demo project](./docs/install-a-demo-project.md)
1. [Install a system service](./docs/install-a-system-service.md)
1. [Install Let's Encrypt](./docs/install-lets-encrypt.md)
1. [Remove the demo project](./docs/remove-the-demo-project.md)
1. [Recurrent management tasks](./docs/recurrent-management-tasks.md)
1. [Adding applications](./docs/add-applications.md)
   1. [HTML, CSS & JavaScript](./docs/add-applications-html-css-js.md)
   1. [Node.js & MongoDb](./docs/add-applications-nodejs-mongodb.md)
   1. [React](./docs/add-application-react.md)
1. [The End](./docs/the-end.md)
