# Install MongoDB

## Add the official repository

Add the repository PGP key:

<pre>
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
</pre>

The result should look similar to this:

<pre>
Executing: /tmp/apt-key-gpghome.3yPzQVajCP/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
gpg: key 68818C72E52529D4: public key "MongoDB 4.0 Release Signing Key &lt;packaging@mongodb.com&gt;" imported
gpg: Total number processed: 1
gpg:               imported: 1

</pre>

Add the repository to apt, but be sure to **update the bold MongoDB version** before you run this command. You can get the up-to-date command on the [MongoDB documentation](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#create-a-list-file-for-mongodb). Select the tab "Ubuntu 18.04 (Bionic)" and copy the command.

<pre>
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
</pre>

## Install MongoDB

Update the package lists:

<pre>
sudo apt update
</pre>

Install the `mongodb-org` meta package, which includes the daemon, configuration and init scripts, shell and management tools on the server.

<pre>
sudo apt install mongodb-org -y
</pre>

Create the data directory and set permissions:

<pre>
sudo mkdir -p /data/db
sudo chown mongodb:mongodb /data/db
</pre>

Update the MongoDB configuration to use the new storage engine wirdeTiger.

<pre>
sudo nano /etc/mongod.conf
</pre>

Replace the storage definition with the following lines:

<pre>
# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb
  engine: wiredTiger
</pre>

Start the MongoDB daemon and check the status:

<pre>
sudo systemctl start mongod
sudo systemctl status mongod
</pre>

The output of the status check should look similar to this:

<pre>
mongod.service - MongoDB Database Server
   Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
   Active: active (running) since Sun 2018-12-16 15:41:06 UTC; 7s ago
     Docs: https://docs.mongodb.org/manual
 Main PID: 18528 (mongod)
   CGroup: /system.slice/mongod.service
           └─18528 /usr/bin/mongod --config /etc/mongod.conf
</pre>

Add the MongoDB daemon to system startup, so that MongoDB starts automatically every time the machine boots:

<pre>
sudo systemctl enable mongod
</pre>

## Add an administrative user

To add a new user for MongoDB, we will connect to the Mongo shell:

<pre>
sudo mongo
</pre>

The output of the shell will have 2 warnings. The first one tells us that there could be a better filesystem to use, which we can ignore, as this is not important for our small server setup. The second one says that we should set some permissions, to ensure secure connections to the database.

<pre>
2018-12-16T15:55:24.273+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2018-12-16T15:55:24.273+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2018-12-16T15:55:25.194+0000 I CONTROL  [initandlisten] 
2018-12-16T15:55:25.194+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2018-12-16T15:55:25.194+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
</pre>

To get rid of the access warning, we will add an administrative user with the name "databaseManager" and a secure password.

<pre>
use admin
db.createUser(
  {
    user: "databaseManager",
    pwd: "<b>{your secure password}</b>",
    roles: [ { role: "root", db: "admin" } ]
  }
);
</pre>

Type `exit` and press [Enter] to leave the client.

## Enabling Authentication

Open the mongodb.conf file with <a href="https://github.com/noreading/simple-node-server#basic-nano-commands" target="_blank">nano</a>:

<pre>
sudo nano /etc/mongod.conf
</pre>

In the "#security" section we remove the hash to enable it and add the authorization setting:

<pre>
security:
  authorization: enabled
</pre>

Restart the daemon to activate authorization:

<pre>
sudo systemctl restart mongod
</pre>

## Verify authorization

Open the Mongo shell:

<pre>
sudo mongo
</pre>

Select the admin database:

<pre>
use admin
</pre>

Try to authenticate as the admin user:

<pre>
db.auth('databaseManager', '<b>{your secure password}</b>')
</pre>

If the result is not only "1", something went wrong copy &amp; pasting.
This is what the full verification should look like:

<pre>
> use admin
switched to db admin
> db.auth('databaseManager', '<b>{your secure password}</b>')
1
> exit
bye
</pre>

You can get more information about user authorization in the [Official Documentation](https://docs.mongodb.com/manual/tutorial/enable-authentication/#user-administrator) if you'd like to know more.

---

**Next:** [Install nginx](./install-nginx.md)
