# Remove the demo project

Now that you've finished the basic setup, here's a small cleanup guide, if you want to get rid of things that you might not need anymore.
After removing the demo project, we'll see how to setup your own applications for different scenarios.

## Stop and remove the service

Remove the process from the init system:

<pre>
sudo systemctl stop boilerplate.<b>{your domain}</b>
sudo systemctl disable boilerplate.<b>{your domain}</b>
sudo rm /etc/systemd/system/boilerplate.<b>{your domain}</b>.service
</pre>

## Remove the database and user

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

exit;
</pre>

## Remove nginx configuration

Remove the configuration file:

<pre>
sudo rm /etc/nginx/conf.d/boilerplate.<b>{your domain}</b>
</pre>

Restart the webserver:

<pre>
sudo service nginx restart
</pre>

## Remove project files

Remove the web root:

<pre>
sudo rm -rf /var/www/node/boilerplate.<b>{your domain}</b>
</pre>

## Remove the SSL certificate

Remove the certificate using the Certbot:

<pre>
sudo certbot delete -d boilerplate.{your domain}
</pre>

---

**Next:** [Recurrent management tasks](./recurrent-management-tasks.md)
