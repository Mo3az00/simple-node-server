# Add a new user

## Get the IP address

Copy the IP address of your new droplet from the [droplet management](https://cloud.digitalocean.com/droplets) page. If you click on the IP address it will automatically be copied to your clipboard.

<img src="./images/digitalocean-droplet-copy-ip.png" alt="Screenshot: Copy the IP address of the droplet" width="500">

## Login to your server

The commands of this tutorial can be run in any terminal on Ubuntu or Mac computers. For Windows you need a linux shell like [Git Bash](https://git-scm.com/downloads), [Cygwin](http://www.cygwin.com/) or the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about).

Use SSH to login:

<pre>
ssh root@<b>{ip address}</b>
</pre>

## Add a new user

It's common to use your real name as username, so that it's easy to remember. If you want to manage a server with multiple users it can be a benefit to use concatenated names like "dhanke" for "Dominik Hanke" as multiple people could have the same first name.

If you're managing the server alone, the easiest thing to remember is your first name.

### Create the user and set a secure password

<pre>
useradd -m -s /bin/bash <b>{username}</b>
passwd <b>{username}</b>
</pre>

### Add sudo access

Open the [sudo](https://www.howtoforge.com/tutorial/sudo-beginners-guide/) configuration in the nano editor <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">(basic nano commands)</a>:

<pre>
nano /etc/sudoers
</pre>

Enable **sudo access** for the new user by adding this two lines.

<pre>
# Allow user to execute any command without password prompt
<b>{username}</b> ALL=(ALL) NOPASSWD:ALL
</pre>

> The the comment beginning with `#` could be ommited, but its always a good practise to comment your changes because such stuff is not in source control:

The result should look something like this:

<img src="./images/server-edit-sudo.png" alt="Screenshot: Edit the /etc/sudoers" width="650"/>

Exit nano pressing `[Ctrl] + [x]` together (<a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano reference</a>).

When nano asks if you want to save the modified buffer, enter "y" and press `[Enter]` twice.

### Add your SSH key

Change your terminal session to that user:

<pre>
su - <b>{username}</b>
</pre>

Create the SSH directory:

<pre>
mkdir .ssh &amp;&amp; chmod 700 .ssh
</pre>

Create an **authorized_keys** file:

<pre>
touch .ssh/authorized_keys &amp;&amp; chmod 600 .ssh/authorized_keys
</pre>

To read your local public key (if youse use only one) you can use the `cat` command or open the file in your editor of choice.

<pre>
cat ~/.ssh/id_rsa.pub
</pre>

Add the SSH key to the `authorized_keys` file in <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="\_blank">nano</a>.

<pre>
nano .ssh/authorized_keys
</pre>

**IMPORTANT**:  
Before you log out from the server, open a second terminal and try to login as the new user to check if everything works as expected:

<pre>
ssh <b>{username}</b>@<b>{ip address}</b>
</pre>

Then press `[Ctrl] + [d]` twice, to logout of the terminal where you are logged in as root user.

## Update packages

As the server is new and the images prepared by DigitalOcean are not updated every day, we should do an initial update of all packages.

<pre>
sudo apt update && sudo apt upgrade -y
</pre>

## Clone the starter files

Clone this repository into your home folder, to get files that you can edit and / or copy to their destination. This will speed up the configuration in the following steps of this tutorial.

<pre>
cd ~
git clone https://github.com/noreading/simple-node-server.git
</pre>

---

**Next:** [Adding security](./add-security.md)
