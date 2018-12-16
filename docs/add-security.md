# Adding Security

## Block root access

**IMPORTANT:**

> If you are working/logged in as `root` (bad-practise) this can lead to loosing control over the server, that's why we want to change this now!

But, while we are working on the setup, always keep a window open where you are logged in as root user. This way you can undo changes if there are problems with the new setup because of copy & paste or other errors.

Open the SSH configuration with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/ssh/sshd_config
</pre>

Search (`[Ctrl] + [w]`) for the string "PermitRootLogin" and change the value from "yes" to "no":

<pre>
PermitRootLogin no
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

Restart the SSH service:

<pre>
sudo systemctl restart ssh
</pre>

## Install fail2ban

Install fail2ban and sendmail:

<pre>
sudo apt-get install fail2ban sendmail -y
</pre>

You might see the following warning in the terminal. Please ignore it, as this is absolutely okay during the first setup of fail2ban on a new machine.

<pre>
Warning: 3 database(s) sources
	were not found, (but were created)
	please investigate.
</pre>

Create a local `fail2ban.conf` file using <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>.

<pre>
sudo nano /etc/fail2ban/fail2ban.local
</pre>

Paste the following settings into the new file:

<pre>
[Definition]

# Set the log level output.
#
#   CRITICAL
#   ERROR
#   WARNING
#   NOTICE
#   INFO
#   DEBUG

loglevel = WARNING
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

Create a local jail.conf file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/fail2ban/jail.local
</pre>

Paste the following settings into the new file and **don't forget** to replace the placeholders:

<pre>
[DEFAULT]

# "bantime" is the number of seconds that a host is banned.
bantime = 1200

# A host is banned if it has generated "maxretry" during the last "findtime" seconds.
findtime = 600

# "maxretry" is the number of failures before a host get banned.
maxretry = 3

# Destination email address used
destemail = <b>{your email address}</b>

# Sender email address used solely for some actions
sender = root@<b>{your hostname}</b>

[ssh]

enabled  = true
port     = ssh
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

Restart the fail2ban service:

<pre>
sudo systemctl restart fail2ban
</pre>

## Setup the `ufw` firewall

You can read more about the uncomplicated firewall [here](https://wiki.ubuntu.com/UncomplicatedFirewall)).

Run the following commands to disallow all traffic that is not coming to a webserver or for the SSH connection:

<pre>
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
</pre>

Enable the firewall and accept the warning, that you might kick yourself out, as we won't do that.

<pre>
sudo ufw enable
</pre>

Check the configuration:

<pre>
sudo ufw status
</pre>

The result should look similar to this:

<pre>
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
80                         ALLOW       Anywhere
443                        ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
80 (v6)                    ALLOW       Anywhere (v6)
443 (v6)                   ALLOW       Anywhere (v6)
</pre>

If you want to see all ufw commands, run the following:

<pre>
sudo ufw --help
</pre>

## Install logwatch

<pre>
sudo apt-get install logwatch -y
</pre>

Open the configuration:

<pre>
sudo nano /usr/share/logwatch/default.conf/logwatch.conf
</pre>

Search for the string "MailTo" and change the value from "root" to your own email address:

<pre>
MailTo = <b>{your email}</b>
</pre>

Search for the string "MailFrom" and change the value to include the hostname:

<pre>
MailFrom = Logwatch on <b>{your hostname}</b>
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

## Harden the network layer

Open the network configuration file:

<pre>
sudo nano /etc/sysctl.conf
</pre>

Remove the hash of in front of the following lines:

<pre>
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1

net.ipv4.tcp_syncookies=1

net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0

net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0

net.ipv4.conf.all.log_martians = 1
</pre>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

---

**Next:** [Install Node.js](./install-nodejs.md)
