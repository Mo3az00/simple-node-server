# Installe Node.js

## Install the package

Run the following commands, but be sure to __update the node version__ in the curl command. As of today (10.02.2018) version 8 is the current LTS release. You can always find the current TLS on the [Node.js homepage](https://nodejs.org/en/).

```
cd ~
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
chmod 755 nodesource_setup.sh
sudo ./nodesource_setup.sh
sudo apt-get install nodejs
rm nodesource_setup.sh
```

## Verify installation

Run the following command:  
```
node -v && npm -v
```

The result should look similar to this:  
```
v8.9.4
5.6.0
```


<div style="border-top: 2px solid #a0a0a0; margin-top: 2.5rem; padding-top: 1rem;">
    <div style="float: left">
        &laquo;
        <a href="./add-security.md">
            Adding Security
        </a>
    </div>
    <div style="float: right;">
        <a href="./install-mongodb.md">
            Install MongoDB
        </a>
        &raquo;
    </div>
</div>