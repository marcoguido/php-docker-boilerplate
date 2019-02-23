# Docker boilerplate repository
 This repository contains a basic scaffolding to be used to setup a new PHP (>= 7.0) project, with the aid of various DBMS (PostgreSQL and MySQL).

In order to use this configuration, follow these steps:

1. Install [Docker desktop](https://www.docker.com/products/docker-desktop) if running either Windows or MacOS, only the [Docker engine](https://www.docker.com/products/docker-engine) if on Linux
2. In the terminal, run `docker-machine create default --driver=virtualbox` to create a new docker-machine (step needed for Mac users, if it's the first docker project)
3. Get the docker VM IP with `docker-machine ip default` and append it to your hosts file (`/etc/host` in UNIX world, `C:\Windows\System32\drivers\etc\hosts` if running Windows) mapping it to the hosts you want to expose like this ```192.168.99.100		hub.maize.test```
4. Set docker variables according to `.env.example` by creating a new `.env.docker` file in the root directory of the project
5. In `.docker/webserver/Dockerfile` edit line `16` by setting your own credentials (not strictly required, still it's a good thing to do)
6. Depending on the webserver you want to use, clone the default scaffolding of the virtualhosts file either of `nginx` or `apache` in `docker/webservers/apache/conf/vhosts` or `docker/webservers/nginx/conf`
7. `cd` into the working directory via terminal
8. Clone all the modules of the ecosystem inside this directory
9. Run in terminal `rm -rf .git`
10. Execute in terminal `docker-compose up --build` in order to build all the stuff
11. Now that everything is built, we can start the dev environment by running `docker-compose up -d php redis {mysql,postgres} {apache,nginx}` (pick only one between the available dbms and webservers)
12. Open PHPStorm and head to the settings to configure XDebug
	- Head to `Preferences | Languages & Frameworks | PHP` and click on the button `...` in order to configure a new interpreter
	- Click on the `+` button choosing `From Docker, Vagrant, VM, etc`
	- Choose `Docker` from the radio button choices
	- Add a new server by clicking on `New...` in "Server" row
	- In the dropdown relative to "Image name", choose `PROJECT_app:php`
	- Click on `Ok` and wait for the IDE to check that everything is ok
	- Click on `Ok` once again to head back to the main settings window
	- On the line `Docker container`, click on the folder icon
	- In the `Volumne bindings` box edit the line referring to `/opt/project` and substitute the "Container path" value with `/var/www/html/PROJECT-DIR`, where `PROJECT-DIR` is the directory containing one of the modules of the project
	- Click on `Apply` and head to the section `Preferences | Languages & Frameworks | PHP | Debug`
	- Update the `Remote port` value to `9001` (or the one you chose in the `.env` file for the `PHP_XDEBUG_REMOTE_PORT` variable) then click on `Apply` again
	- Head to `Preferences | Languages & Frameworks | PHP | Servers` and add a new server with the `+` icon
	- Name it `docker-server` and as the host, set the url of the current site, then check the `Use port mappings` checkbox
	- Under the "Project files" section, add as the `Absolute path on the server`, paste `/var/www/html/PROJECT-DIR` related to the root directory of the project
	- Repeat the last 2 steps for each virtual host that a module has to expose
	- After the last iteration, click on `Apply`
13. On each system reboot, in terminal run `docker-machine start` then `cd` to this directory (the one containing the `docker-compose.yml` file) and execute `docker-compose up -d php redis {mysql,postgres} {apache,nginx}` (chose only one between the dbms and webserver options).
14. Access the main application container when needed by typing in a terminal pointing to this directory (the one containing the `docker-compose.yml` file) `docker-compose run php bash`
