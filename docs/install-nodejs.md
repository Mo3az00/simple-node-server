# Installe Node.js

## Install the package

Run the following commands, but be sure to __update the node version__ in the curl command. As of today (10.02.2018) version 8 is the current LTS release. You can always find the current TLS on the [Node.js homepage](https://nodejs.org/en/).

<pre>
cd ~
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
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
v8.9.4
5.6.0
</pre>

---
__Next:__ [Install MongoDB](./install-mongodb.md)
