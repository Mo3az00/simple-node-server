# Recurrent management tasks

When you're running your own server you should be aware that you have to do some recurring tasks every now and then.

You have to update installed packages and check if the update might break your applications. Upgrading the Node.js version on a server can easily break some of your applications, so never forget to check what will be updated, before you really run the upgrade.

We will install some helpers to make your life as an admin a bit easier.

## Install unattended updates

There's a software package to automatically install important security updates without asking or waiting for an administrator. This is especially useful for security updates, as zero-day attacks can be prevented.

Install the needed package:  
<pre>
sudo apt install unattended-upgrades
</pre>

Open the config file:  
<pre>
sudo nano 
</pre>

Update the following line to get details about updates in your inbox:  
<pre>
...
Unattended-Upgrade::Mail "<b>{your email}</b>";
...
</pre>

We can enable the automatic updates by editing the following file:  
<pre>
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
</pre>

Update the config to this:  
<pre>
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
</pre>

## Install package monitoring

Install the "apticron" package, that send you emails about updates that are not installed automatically:  
<pre>
sudo apt-get install apticron
</pre>

Edit the config file and add your email address:  
<pre>
sudo nano /etc/apticron/apticron.conf
</pre>
<pre>
EMAIL="<b>{your email}</b>"
</pre>

## Update packages manually

To run manual package updates you can use the following command:  
<pre>
sudo apt-get update &amp;&amp; sudo apt-get upgrade
</pre>

Some packages will only be installed when you run the next command on top. But be careful, as this are most likely packages that could break your system. This will, in 99.9% of all cases, not happen, but it's always good to have a look at your apticron mails. They will tell you what was changed and if it's an important or breaking update.
<pre>
sudo apt-get dist-upgrade
</pre>

---
__Next:__ [The End](./the-end.md)