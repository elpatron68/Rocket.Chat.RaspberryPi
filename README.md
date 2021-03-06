[![Rocket.Chat on Pi Logo](https://raw.githubusercontent.com/Sing-Li/bbug/master/images/raspberry-logo.png)](https://www.raspberrypi.org/)

* Raspberry Pi 2 is the tiny $30 quad-core computer that revolutionalized affordable servers
* Rocket.Chat is the popular high performance, large capacity, open source web chat platform that rocked the server world 

You can now run a private chat server on your Pi for:
* family
* social or hobby group
* sports team
* school
* office

![Picture of mobile Rocket.Chat in action](https://cloud.githubusercontent.com/assets/11763113/11993320/ccdcf296-aa72-11e5-9950-e08f7a280516.png)

Enjoy Rocket.Chat features including:
* Video and audio chat
* Share photos and voice messages
* Share streaming music and video links
* iOS app for iPhones and iPads
* App for Android phones and tablets 
* Desktop app for Windows, MacOSX and Linux
* Operate in 22 different languages
* Multiple Rooms
* Direct Messages
* Private Groups
* Avatars
* Emojis
* Media Embeds
* Link Previews
* Many more ...

### Background

This project adapts the Rocket.Chat server to run on a Raspberry Pi 2 

<img src="https://raw.githubusercontent.com/Sing-Li/bbug/master/images/rockpismal.png" width="480">

Learn about [Rocket.Chat](https://rocket.chat/).

This project is currently work in progress and is undergoing rapid changes.

### Running Rocket.Chat on Pi Server Quick Start

**VERY IMPORTANT**

Make sure you start with a CLEAN INSTALL of **Raspbian JESSIE  -- NOT Wheezy**

Make sure you are using a **Pi 2** -  **NOT a Pi Model B or Model B+ or Model A** (the older Pi models have completely different instruction set - v6 - and require binaries that **WILL NOT** work with this instruction - see [FAQ](https://github.com/RocketChat/Rocket.Chat.RaspberryPi/wiki/Frequently-Asked-Questions) if you absolutely must work with older Pi)

The shortest path to get Rocket.Chat running on your Pi:

#### Get latest Raspian for your Pi

Find download here (this instruction assumes Raspbian Jessie):

https://www.raspberrypi.org/downloads/raspbian/



Use the shell (you do not need the GUI), get the latest fix and updates:

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install git
```

####  Get required node and npm

The version of `node` distributed with Raspian is too old.  `npm` is not included.

The easiest way to get both is to clone from the Meteor universal project.

```
cd $HOME
git clone --depth 1 https://github.com/4commerce-technologies-AG/meteor.git
```

then

`$HOME/meteor/meteor -v`

####  Download the Rocket.Chat binary for Raspberry Pi

````
cd $HOME
mkdir rocketchat
cd rocketchat
curl https://cdn-download.rocket.chat/build/rocket.chat-pi-develop.tgz -o rocket.chat.tgz
tar zxvf rocket.chat.tgz
````

This will download and untar the app in `$HOME/rocketchat`

#### Get a compatible MongoDB instance

Current available mongodb versions on Raspbian are too old for Rocket.Chat. Hopefully the
situation will change shortly.

Meanwhile, you can use a MongoDB service provider on the Internet.  MongoLab offers 
free sandbox databases that can be used with Rocket.Chat.  Create a free account and
database here:

https://mongolab.com/

Create a user and give it write access to the database.  Note the Mongo URL, you will
need it next.

#### Install dependencies and start Rocket.Chat

```
cd $HOME/rocketchat/bundle/programs/server
$HOME/meteor/dev_bundle/bin/npm install
cd $HOME/rocketchat/bundle
```

The following ALL on one single line, with spaces to separate the environment variables:
```
PORT=3000  ROOT_URL=http://localhost:3000   MONGO_URL=mongodb://<user>:<password>@<host>:<port>/dataurlfrommongolabs    $HOME/meteor/dev_bundle/bin/node main.js
```

Wait until the server fully starts. About a minute.

#### Access your Rocket.Chat server and create the Administrative user

Point a browser on your PC to your Raspberry Pi:

http://rasp pi host IP:3000/

#### Install as supervisor
Installing Rocket.Chat with supervisor makes shure your chat server starts at system boot and restarts if it crashes. Refer to [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-supervisor-on-ubuntu-and-debian-vps) for more information about Supervisor.  
* Install supervisor
`sudo apt-get install supervisor`

* Create a new configuration file for supervisor
`sudo nano /etc/supervisor/conf.d/Rocket.Chat.conf`

  * Paste this into nano
  ```
  [program:RocketChat]
  command=/home/pi/rocketchat/bundle/start_rcpi.sh
  directory=/home/pi/rocketchat/bundle
  autostart=true
  autorestart=true
  stderr_logfile=/var/log/Rocket.Chat.err.log
  stdout_logfile=/var/log/Rocket.Chat.out.log
  ```
  * Save and exit (Crtl-x , y)

* Create `start_rcpi.sh`
  * `nano $HOME/rocketchat/bundle/start_rcpi.sh`
  * Paste the following into nano:
  ```
  #!/bin/bash
  PORT=3000  ROOT_URL=http://localhost:3000   MONGO_URL=mongodb://<user>:<password>@<host>:<port>/dataurlfrommongolabs  /home/pi/meteor/dev_bundle/bin/node main.js
  ```
  * Change MONGO_URL to match your settings (see above)
  * Save and exit (Crtl-x , y)

* Make `start_rcpi.sh` executable: `sudo chmod +x start_rcpi.sh`  

* Start supervisor:  
  ```
  sudo supervisorctl reread  
  sudo supervisorctl restart  
  ```
* Make sure everything is up and running
  ```
  pi@raspberrypi:~/rocketchat/bundle $ sudo supervisorctl
  RocketChat                       RUNNING    pid 26619, uptime 0:07:09
  ```

#### Put your chat server on the Internet for global access

With your Rocket.Chat server up and running, start another shell - typically (Ctrl-Alt-F2) or (Ctl-Alt-F3).

Login, download and start ngrok (see [ngrok.com](https://ngrok.com) if you need more information):

```
curl  https://dl.ngrok.com/ngrok_2.0.19_linux_arm.zip -o ngrok.zip
unzip ngrok.zip
cd ngrok
./ngrok http 3000
```

Now follow the instruction and give the ngrok link to your friends and family anywhere in the world.  

They can access your server via the ngrok link.  

HINT:  if you want to use the voice and video chat features, make sure you give them the link starting with `https://`

#### Mobile messaging on phones and tablets

Ask your friends to download the Rocket.Chat mobile app on Android PlayStore or the Apple Appstore for their phone and tablets! 

Add your server's ngrok link to the app, and start mobile messaging one another!

#### Stuck?  Need help?

First, check our list of [Frequently Asked Questions](https://github.com/RocketChat/Rocket.Chat.RaspberryPi/wiki/Frequently-Asked-Questions) to see if your question is already included.

If not, create an issue here:     https://github.com/RocketChat/Rocket.Chat.RaspberryPi/issues/new

OR

Come join us at https://demo.rocket.chat/ to get help from friendly  RockOnPi community members and Rocket.Chat dev team.

#### RockOnPi Community meetup 24 x 7

The RockOnPi community gathers at https://demo.rocket.chat/channel/raspberrypi  - and talk Pi !!

#### Makers Ahoy!

Your imagination is your only limit.

Both Raspberry Pi and Rocket.Chat are open source, 100% programmable, 100% Makers-ready!

* get the Raspberry Pi Camera into a Rocket.Chat room
* hook up your home control project to Rocket.Chat
* manage your fleet of drones remotely on the web via RC on Pi

Tell us about your innovative project, or find other collaborators at:

https://demo.rocket.chat/channel/raspberrypi

#### Support this project

Help us spread the word about this project!  

Tell all your Pi and Maker friends!  Show off Rocket.Chat at your next meetup!

Tweet about us, or show off Pi with Rocket.Chat on Facebook!

Order a Rocket.Chat sticker for your [Mac](https://www.stickermule.com/marketplace/10009-rocket-dot-chat-logo), [tablet](https://www.stickermule.com/marketplace/9987-rocket-dot-chat), or [Pi case](https://www.stickermule.com/marketplace/9989-rocket-dot-chat)!

### Where to get the Raspberry Pi 2 server
* [Microcenter](http://www.microcenter.com/product/447313/2_Model_B_Development_Board)
* [Frys](http://frys.com/product/8402328?site=sr:SEARCH:MAIN_RSLT_PG)
* [Adafruit](https://www.adafruit.com/product/2358)
* [Sparkfun](https://www.sparkfun.com/products/13297)
* [Amazon](http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I)
* [Element 14](http://www.element14.com/community/community/raspberry-pi/raspberrypi2)
* [Mercado Livre Brazil](http://lista.mercadolivre.com.br/raspberry-pi-2-1gb#D)
* [RS Japan](http://jp.rs-online.com/web/p/processor-microcontroller-development-kits/832-6274/)

### Where to get Rocket.Chat

Apps for iPhone, iPad, Android, Windows, MacOSX:

https://Rocket.Chat/

Server source code (open source MIT Licensed):

https://github.com/rocketchat/Rocket.Chat

