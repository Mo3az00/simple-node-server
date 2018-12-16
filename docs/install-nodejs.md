# Install Node.js

## Install the package

Run the following commands, but be sure to **update the node version** in the curl command. As of today (15th December 2018) version 10 is the current LTS release. You can always find the current TLS on the [Node.js homepage](https://nodejs.org/en/).

<pre>
cd ~
curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh
chmod 755 nodesource_setup.sh
sudo ./nodesource_setup.sh
sudo apt-get install nodejs -y
rm nodesource_setup.sh
</pre>

## Verify installation

Run the following command:

<pre>
node -v &amp;&amp; npm -v
</pre>

The result should look similar to this:

<pre>
v10.14.2
6.4.1
</pre>

---

**Next:** [Install MongoDB](./install-mongodb.md)
