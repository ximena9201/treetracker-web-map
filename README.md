# Treetracker Web

## Current Milestones and Issue Topics.

Developers please see current milestones here:  
https://github.com/Greenstand/treetracker-web-map/milestones


Big picture UX/UI challenges are tracked at:  
https://github.com/Greenstand/treetracker-web-map/issues?q=is%3Aissue+is%3Aopen+label%3AUX%2FUI

&nbsp;
&nbsp;

## Project Description

Displays location and details of all trees that have been tracked.

Live map is at [www.treetracker.org](https://www.treetracker.org)

For more details see the [Tree Tracker Web Map Wiki] (https://github.com/Greenstand/treetracker-web-map/wiki)

&nbsp;
&nbsp;

## Development Environment Quick Start

We provide a development environment through docker that can run on your local environment.  You can also develop locally without docker if you are willing to install Postgres and postGIS (see 'Developing locally without docker' below)

### Set Up Docker
To run docker on a local machine, you will have to install Docker first. Docker is a linux container technology, so running it on Mac or Windows requires an application with an attached linux VM. Docker provides one for each OS by default.

[Docker for Mac](https://docs.docker.com/docker-for-mac/install/)

You can alternatively install Docker for Mac using homebrew, using the following command

```
$ brew cask install docker
```

[Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
Please note Docker for Windows only works for Windows 10 Enterprise and Windows 10 Pro editions. 

To install on linux, you can run `sudo apt-get install -y docker-ce` but there is [additional setup](https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository) to verify keys, etc.


### Install, build docker containers and go

Clone this repository

```
git clone git@github.com:Greenstand/treetracker-web-map.git
cd treetracker-web-map
```

Place the server's config.js file in the folder server/config/.  You can get this file by asking another contributor for it on Slack.

Place the client's config.js file in the folder client/js/.  You can get this file by asking another contributor for it on Slack.

### To start the dev environment go to the root of the project and execute the following:

```
docker build -f Dockerfile.Nginx  -t treetracker-map-reverse-proxy:latest .
docker build -t treetracker-map-api .
docker-compose up -d
```

You can now view the treetracker web map at http://localhost:8080

### To stop the dev environment use:

```
docker stop treetracker-web-map_reverse-proxy_1 && docker rm treetracker-web-map_reverse-proxy_1
docker stop treetracker-map-api && docker rm treetracker-map-ap
```

Just edit as you normally would to view changes in your development environment.

### Developing locally without docker

1. Install postgres
2. Install postGIS
3. Create a database named 'treetracker'
4. Import our developer seed into your database.  Seed can be downloaded at https://developer-resources.sfo2.cdn.digitaloceanspaces.com/treetracker_developer_seed.sql.gz
5. Configure server/src/config/config.js to point to your local database
6. Install node modules, run npm i in server/ folder
```
npm i
```
7. Install supervisor globally
```
npm i -g supervisor
```
8. Run server.js with CORS restrictions lifted
```
NODE_ENV=dev supervisor server.js
```
9. Open client/js/config.js from the project root.  It should contain only the following 2 lines
```
var configTreetrackerApi = 'http://localhost:8080/api/web/;
```
9. Open the index.html file of the webmap in a web browser.  Use file:/// protocol, not a localhost address.

### Developing without docker, but using online DB.

1. Make sure all npm modules are installed under client and server.
```
cd client
npm i
```

```
cd server
npm i
```

2. Start the server
```
cd server
npm run dev
```

3. Start the client
```
cd client
npm start
```

4. Open the web map in the browser with URL: http://localhost:3000

NOTE: in this way, the server would run on port 3001, and client would run on port 3000.


### MS Windows setup details

Our docker-compose settings for volume mounting don't work out of the box for at least some versions of Windows.  Please see this thread for more information, and update this document with usage details if this solves your issue.

https://github.com/docker/compose/issues/4303


### Alternative development environment for MS Windows (Works on Linux and Mac also)
On Windows, the easiest way to develop and debug Node.js applications is using Visual Studio Code.
It comes with Node.js support out of the box.

https://code.visualstudio.com/docs

&nbsp;
&nbsp;

## Web App installation issues

Ubuntu:
docker-compose - command not found:
 - $ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)"  -o /usr/local/bin/docker-compose
 - $ sudo mv ./docker-compose /usr/bin/docker-compose
 - $ sudo chmod +x /usr/bin/docker-compose

docker-compose - permission denied:
- cd to /usr/bin
- Enter command sudo chmod 777 docker-compose

docker: Got permission denied while trying to connect to the Docker daemon socket
 - $ sudo groupadd docker
 - $ sudo usermod -aG docker $USER

Version in "./dev/docker-compose.yml" is unsupported. 
 - confirm docker-compose is installed
 - $ docker-compose -v 
 - If docker-compose is installed:
 - $ sudo curl -L https://github.com/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
 - $ sudo chmod +x /usr/local/bin/docker-compose
 - if docker-compose is not installed:
 - $sudo apt-get install docker-compose

Error: Failed to load resource: the server responded with a status of 404 (Not Found)
map.js:18 Uncaught ReferenceError: configTreetrackerApi is not defined
    at map.js:18

**Solution**: To Solve this issue, go into the client/js folder, and either change the file config.js.example to config.js, or copy it into a file of the same name.

&nbsp;
&nbsp;

## Clustering Basics

For performance and UX purposes, since this map needs to deal with an enormous amount of trees, a clustering strategy is used to group those trees, showing information in a way that is more digestible for the end-user.

Although this feature is already implemented, performance optimizations are a work in progress.

### Overriding clustering and map initial zoom for testing

When there is a need to tweak the clusterization behavior, the **cluster radius** and **zoom** can be overridden specifying query strings.
For example, if you need to load the map with an initial zoom level of 15, and a radius of 0.001 you will access it like this:

dev.treetracker.org?**zoom=15&clusterRadius=0.001**

To find the correct value for the cluster radius in a given zoom level, play with some ranges between 0.1 and 0.00025. However, feel free to experiment however you like.

When these values are overridden, you can zoom and drag the map freely, while keeping the same clusterization behaviors.

Another useful tool to use in conjunction with this is the web browser's console (in Chrome or Firefox, hit F12 to open it). Whenever the map is updated, current zoom level and cluster radius used will be output to the console, so you have a better idea of what is going on.

Future: 
* Filters and Statistics
* View photo together with tree data
* View planter profile. 

### How to test

We use Jest to build tests.

1. To test client
```
cd client
npm test
```

2. To test server
```
cd server
npm test
```
