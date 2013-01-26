Setup a Linux (Ubuntu) VM on Azure to run Node.js with Upstart
===================

*Step-by-step guide*
  
Setting up an Azure VM with Ubuntu to run Node.js Server.  
  

*Setup & SSH:*
  * After creating the VM, the SSH user is ‘azureuser’
  * In Terminal, run: 

```shell
ssh azureuser@<your-service-name>.cloudapp.net
```


*Installing Apache and Git Core*
```shell
sudo apt-get update
```

*Install the dependencies*
```shell
sudo apt-get install g++ curl libssl-dev apache2-utils
sudo apt-get install git-core
```

*Run the following commands:*

```shell
git clone git://github.com/joyent/node.git
cd node/
./configure 
make
ls -l
node
sudo make install
```

Explanations:  
* git clone creates a copy of git repository node's source code is in
* cd node/ changes directory to the one you just created with those files
* ./configure checks for dependencies and creates a makefile
* make executes that makefile, which results in compiling the source code into binary executable(s), libraries and any other outputs
* ls -l lists the files in the current directory
* node runs the node binary executable you just compiled from source, to ensure the compilation was successful
* sudo make install copies the files you just created from the current directory to their permanent homes, /usr/local/bin and such

*Instal NPM*
```shell
sudo apt-get install nodejs npm
```

*Install Express*
```shell
cd ~
sudo mkdir <application folder name>
cd <application folder name>
sudo npm install express -g
```

*Upstart & Server Environment Variables*
Create a node-upstart.conf (file name doesn't matter) in the /etc/bin directory
```shell
#!upstartart
description "node.js server"
author      "Ludo Goarin"

# until we found some mounts weren't ready yet while booting:
start on (local-filesystems and net-device-up)
stop on shutdown

# Automatically Respawn:
respawn
respawn limit 99 5

script
    # Not sure why $HOME is needed, but we found that it is:
    export HOME="/home/azureuser"
    export NODE_ENV="production"
    export PORT="80"
    exec /usr/local/bin/node /home/azureuser/<path to your application app.js file> >> /var/log/node-upstart.log 2>&1
end script
```
The above will ensure your Node.js app runs on port 80 and will start or re-start automatically in case of crash or server reboot.  


Test Upstart
```shell
start node-upstart.conf
```
Restart your server to make sure your app is running correctly
```shell
status node-upstart
```
The above command should return something like: node-upstart start/running, process 886

*GitHub Deployment*
```shell
git clone https://github.com/<username>/<repo>.git
Username: <username>
Password: <password>
```

