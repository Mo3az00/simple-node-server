# Recurrent management tasks

When you're running your own server you should be aware that you have to do some recurring tasks every now and then.

You have to update installed packages and check if the update might break your applications. Upgrading the Node.js version on a server can easily break some of your applications, so never forget to check what will be updated, before you really run the upgrade.

We will install some helpers to make your life as an admin a bit easier.

## Install unattended updates

There's a software package to automatically install important security updates without asking or waiting for an administrator. This is especially useful for security updates, as zero-day attacks can be prevented.

Open the config file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
</pre>

Update the following line to get details about updates in your inbox. You need to remove the `#` and add your email address.

<pre>
...
Unattended-Upgrade::Mail "<b>{your email}</b>";
...
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

We can enable the automatic updates by editing the following file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
</pre>

Update the config to this:

<pre>
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

## Install package monitoring

Install the "apticron" package, that send you emails about updates that are not installed automatically:

<pre>
sudo apt-get install apticron -y
</pre>

Edit the config file with nano <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>and add your email address:

<pre>
sudo nano /etc/apticron/apticron.conf
</pre>

Update the following line with your email address:

<pre>
EMAIL="<b>{your email}</b>"
</pre>

## Update packages manually

To run manual package updates you can use the following command:

<pre>
sudo apt update &amp;&amp; sudo apt upgrade &amp;&amp; sudo apt autoremove
</pre>

Some packages will only be installed when you run the next command on top. But be careful, as this are most likely packages that could break your system. This will, in 99.9% of all cases, not happen, but it's always good to have a look at your apticron mails. They will tell you what was changed and if it's an important or breaking update.

<pre>
sudo apt dist-upgrade &amp;&amp; sudo apt autoremove
</pre>

---

**Next:** [Adding applications](./adding-applications.md)
