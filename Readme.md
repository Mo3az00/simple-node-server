# WORK IN PROGRESS

Do not use this manual, until this comment is removed, as it's work in progress and incomplete.  
Feel free to come back later and give it a try. :-)

This tutorial is based on [Hetzner Cloud Servers](https://www.hetzner.com/cloud?country=us), but you can use any root or virtual server with an [Ubuntu 16.04](http://releases.ubuntu.com/16.04/) image.

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
Domain:
your-domain.com

Hostname:
node1.prod.your-domain.com

User:
peter

Password:
l0gM3!N?7&hc0m3L3tM3!nN0Wpl§$5%3

MongoDB User:
dbManager

MongoDB Password:
my53cur3L=g1nF/rd43m-nm0ng0DB
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

# Contents

1. [What will I get from this tutorial?](./docs/what-to-get-from-this-tutorial.md)
1. [Registering a free Hetzner account](./docs/registering-a-free-hetzner-account.md)
1. [Adding a project and SSH key](./docs/adding-a-project-and-ssh-key.md)
1. [Creating a small server](./docs/creating-a-small-server.md)
1. [Adding a new user](./docs/adding-a-new-user.md)
1. [Adding security](./docs/adding-security.md)
1. [Install Node.js](./docs/install-nodejs.md)
1. [Install MongoDB](./docs/install-mongodb.md)
1. [Install nginx](./docs/install-nginx.md)
1. [Install demo project](./docs/install-demo-project.md)
1. [Install a system service](./docs/install-system-service.md)
1. [Install Let's Encrypt](./docs/install-lets-encrypt.md)
1. [Cleanup](./docs/cleanup.md)