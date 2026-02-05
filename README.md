# epicbox-nodejs - Installation Files for 3.0.1

NOTICE - This is for a manual install of mongodb and epicbox. For an updated automated Docker solution go to repo
https://github.com/EpicCash/epic-epicbox-docker

Files for Installing the epicbox Server

Read the epicbox Installation pdf for specifics

Includes binaries as well as sample config and service files

## Installation:

Install and Run an Epicbox Relay Server

If you would like to run a local epicbox server for your state or country, please follow these
steps. It is assumed you are logged in as root or running sudo su.

Prepare a server class computer running linux (Ubuntu 20.04, Mint 20.3, VPS is OK)

Create user 'epic'
cd /home/epic

Download epicbox.zip and unzip into /home/epic

chown epicbox, epicboxlib, config.json to epic:epic and make binaries executable (if not already) in /home/epic

Create a DNS entry pointing to epicbox.your-domain

Install nginx

In epicbox.nginx: modify domain to epicbox.your.domain then copy to /etc/nginx/sites-enabled
- $ systemctl restart nginx

Install certbot and run to update the nginx file:
- $ apt install certbot
- $ apt install certbot python3-certbot-nginx
- $ certbot --nginx --redirect -d your.domain -d epicbox.your.domain -m youremailaddress --agree-tos --no-eff-email

Restart nginx service
- $ systemctl restart nginx

Install mongodb: https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition

Copy mongod.conf to /etc
- $ systemctl enable mongod
- $ systemctl start mongod
- $ systemctl status mongod

Monitor mongodb with 'journalctl -fu mongod.service'

Epicbox Server:

In config.json: change "epicbox_domain": "epicbox.your.domain" to your own domain

- $ cp epicbox.service /etc/systemd/system
- $ systemctl daemon-reload
- $ systemctl start epicbox
- $ systemctl enable epicbox

Monitor epicbox with 'journalctl -fu epicbox'

Create mongodb index (after epicbox has processed one transaction to create the epicbox db and slates collection):

- $ mongosh
```
use epicbox
db.slates.createIndex({queue:1, made:1, createdat: 1})
db.slates.createIndex({messageid:1, made:1})
db.slates.createIndex({ "createdat": 1 }, {expireAfterSeconds: 604800 })
quit()
``` 
Note: epicbox created from app_mongo.js at https://github.com/EpicCash/epicboxnodejs-source/tree/3.0.1

(run pkg -t node18 -o epicbox app_mongo.js)

## Update Existing Server:

- $ systemctl stop epicbox

Download epicbox.zip and unzip epicbox and epicboxlib into /home/epic as user: epic ($ su epic)

Check config.json in .zip for changes and unzip if it needs updating

In config.json: change "epicbox_domain": "epicbox.your.domain" to your own domain (if updated)

- $ systemctl start epicbox


