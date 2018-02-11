# Cleanup

Now that you've finished this tutorial, here's a small cleanup guide, if you want to get rid of things that you might not need anymore.

Feel free to send me feedback for the tutorial and have fun with your server!  
[support@dominik-hanke.de](mailto:os@dominik-hanke.de)

Bye, bye!

Dominik

## Remove the starter files

You can keep the starter files for reference, when addint new applications and configurations or delete them.  
If you want to delete the starter files and the repository, run the following command:  
<pre>
rm -rf ~/simple-node-server/
</pre>

## Remove the demo project

The following steps descripe how to to remove the demo project completely after successfully testing all important features of your new server.

### Stop and remove the service

Remove the process from the init system:  
<pre>
sudo systemctl stop node-boilerplate.<b>{your domain}</b>
sudo systemctl disable node-boilerplate.<b>{your domain}</b>
sudo rm /etc/systemd/system/node-boilerplate.<b>{your-domain}</b>
</pre>

### Remove the database and user

Connect to the Mongo shell:  
<pre>
sudo mongo
</pre>

Authenticate as root user:  
<pre>
use admin
db.auth('databaseManager', '<b>{your db admin password}</b>')
</pre>

Remove the database:  
<pre>
use boilerplate
db.runCommand( { dropAllUsersFromDatabase: 1, writeConcern: { w: "majority" } } )
db.dropDatabase()
</pre>

Remove the user:  
<pre>
use admin
db.dropUser('boilerplate')
exit
</pre>

### Remove nginx configuration

Remove the configuration file:  
<pre>
sudo rm /etc/nginx/conf.d/boilerplate.<b>{your-domain}</b>
</pre>

Restart the webserver:  
<pre>
sudo service nginx restart
</pre>

### Remove files

Remove the web root:  
<pre>
sudo rm -rf /var/www/node/boilerplate.<b>{your domain}</b>
</pre>