# Cleanup

Now that you've finished this tutorial, here's a small cleanup guide, if you want to get rid of things that you might not need anymore.

Feel free to send me feedback for the tutorial and have fun with your server!  
[support@dominik-hanke.de](mailto:os@dominik-hanke.de)

Bye, bye!

Dominik

## Remove the starter files

You can keep the starter files for reference, when addint new applications and configurations or delete them.  
If you want to delete the starter files and the repository, run the following command:  
```
rm -rf ~/simple-node-server/
```

## Remove the demo project

The following steps descripe how to to remove the demo project completely after successfully testing all important features of your new server.

### Stop and remove the service

Remove the process from the init system:  
```
sudo systemctl stop node-boilerplate.{your domain}
sudo systemctl disable node-boilerplate.{your domain}
sudo rm /etc/systemd/system/node-boilerplate.{your-domain}
```

### Remove the database and user

Connect to the Mongo shell:  
```
mongo
```

Authenticate as root user:  
```
use admin
db.auth('databaseManager', '{your db admin password}')
```

Remove the database:  
```
use boilerplate
db.runCommand( { dropAllUsersFromDatabase: 1, writeConcern: { w: "majority" } } )
db.dropDatabase()
```

Remove the user:  
```
use admin
db.dropUser('boilerplate')
exit
```

### Remove nginx configuration

Remove the configuration file:  
```
sudo rm /etc/nginx/conf.d/boilerplate.{your-domain}
```

Restart the webserver:  
```
sudo service nginx restart
```

### Remove files

Remove the web root:  
```
sudo rm -rf /var/www/node/boilerplate.{your domain}
```