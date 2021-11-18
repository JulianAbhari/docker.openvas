### Hello, these are steps I used to successfully install Docker and the OpenVAS/Greenbone vulnerability scanner on my Ubuntu virtual machine.

## 1.
Use this command in your ubuntu terminal to update your repositories:
`sudo apt-get update`

## 2.
After using it enter this command to upgrade your repositories (it might take a while):
`sudo apt-get upgrade`


## 3.
Now that all repositories are at the latest version you can actually install docker. The fastest way I was able to install docker was by using this command:
`sudo apt install docker.io`


## 4.
In order to activate docker run this command:
`sudo service docker start`


## 5.
To make sure docker is running, use this command:
`sudo service docker status`

## 6.
Now to install a container to hold OpenVAS. Enter this command:
`sudo docker run -d -p 443:443 --name openvas mikesplain/openvas`

## 7.
The container should now be installed and if you connect to localhost through your web-browser you should be able to see the OpenVAS dashboard. I simply loaded up firefox on my Ubuntu VM and entered this URL address into the URL input bar:
`https://localhost/`

## 8.
Once at the OpenVAS login, simply enter the default username and password and then you’ll be redirected to the dashboard. The default login is this:
`login: admin`
`password: admin`

## 9.
Now to actually use OpenVAS to run a vulnerability scan you need to start by clicking the “configuration” dropdown menu, then select the targets. Once there click the star at the top left to create a new target to scan.
I gave this target the name “Virtual Subnet” and specified the IP address as:
`10.0.1.0-255`

## 10
After setting up a target, hover over the “scans” dropdown menu, then select “tasks”. After loading the tasks, click the star icon in the top left corner and create a new task. 
Give this task the name of “virtual subnet scan” and be sure that scan target is set to the “virtual subnet” target.

## 11
After creating this task you should see an “actions” bar to the far right of your newly created task, and there’s a play button that will run the task. Click the button to do a vulnerability scan.

## 12
Finally, in order to create a docker-compose.yml file for OpenVAS, we need to install docker-compose on our VM.
I did this using this command:
`sudo apt install docker-compose`

## 13
After installing I checked the docker-compose version to make sure it worked:
`docker-compose -v`

## 14
I then made a directory for the openvas docker-compose.yml file and changed my directory to the newly created one:
`mkdir openvas`
`cd openvas`

## 15
I then used touch to create a new docker-compose.yml file:
`touch docker-compose.yml`

## 16
Now to add info in the file:
`nano docker-compose.yml`

## 17
I added in this text block that I found online for openvas using docker 3.3
```
version: “3.3”
services:
openvas:
     restart: always
     image: mikesplain/openvas
     hostname: openvas
     expose:
       - "443"
     volumes:
       - "./data/openvas:/var/lib/openvas/mgr/"
     environment:
       # CHANGE THIS !
       OV_PASSWORD: admin
     labels:
        deck-chores.dump.command: sh -c "greenbone-nvt-sync; openvasmd --rebuild --progress"
        deck-chores.dump.interval: daily

```
## 18
Finally I ran this in the same directory as the containers:
`docker-compose up -d`

