# Angular Tour Of Heroes with Docker

This example shows how to run an angular app inside a docker container. 

The angular app is forked from [https://github.com/osechet/angular-tour-of-heroes-webpack](https://github.com/osechet/angular-tour-of-heroes-webpack), which contains a basic angular tutorial using webpack to build distributable release files. The app is then mounted in a docker container which uses nginx to serve static html & javascript [(see the alpine:nginx image)](https://hub.docker.com/_/nginx/)

There are many tutorials that show how to serve html using nginx in a docker container; this has additional steps for overcoming issues when using Docker Toolbox on Windows, which is required when docker itself can't be run on the host machine (for example, because VirtualBox is already in use, preventing hyper-v from doing its thing).

## Prerequisites
This was created using a test environment with the following relevant dependencies installed:
* Node v6.10.3
* NPM v4.6.1
* TSD v0.6.5 (typescript definitions)
* VirtualBox v5.1.18
* Docker Toolkit v17.04.0-ce

## Clone this repository

```bash
git clone https://github.com/crejb/angular-tour-of-heroes-webpack.git
cd angular-tour-of-heroes-webpack
```

## Install npm packages

> See npm version notes above

Install the npm packages described in the `package.json` and verify that it works:

```bash
npm install
npm start
```

The `npm start` command first compiles the application, 
then uses webpack to bundle it and run a development web server.
Webpack watches for file changes.

Shut it down manually with `Ctrl-C`.

## Build the distribution files

Run the script to create the dist directory with the static files for distribution:

```bash
npm run build.prod
```

## Prepare the docker container
The `docker` folder contains 2 files:
- `default.conf`: the nginx config file that will be used by the container
- `docker-compose.yml`: defines how docker should start the container. 
```
web:
  container_name: nginx-example # Tag the container with a name to make it easier to stop
  image: nginx:alpine # The base image for the container
  ports: # Port forwarding from the docker host to the docker container
    - "8087:8087"
  volumes: # Mount directories from the docker host inside the docker container
    - [directory]/default.conf:/etc/nginx/conf.d/default.conf # nginx config file
    - [directory]/dist:/usr/share/nginx/html # directory containing the static html files for the angular app
```

In the docker-compose file, [directory] will need to be replaced with the correct location on the machine.

**Important caveats for running with Docker Toolbox and VirtualBox**

* Path must be absolute
* Path must be in unix format
* Path must be in a folder shared by virtualbox from the host to the client (default c:\Users)
* Path is case sensitive

For example, copy the angular dist folder and nginx default.conf to C:\Users\nginx-example. Then, change [directory] to /c/Users/nginx-example

## Start the docker container

In the docker toolbox command prompt, start the container as a background process by running:

```bash
docker-compose up -d
```

The app should now be hosted and available to access from the browser.

When the docker toolbox starts, it prints out the IP of the virtual docker host. Fo example, in the output below, the IP is 192.168.99.101:

```
                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/

docker is configured to use the default machine with IP 192.168.99.101
For help getting started, check out the docs at https://docs.docker.com
```

The app should be available at http://192.168.99.101:8087