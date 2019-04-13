# Docker boilerplate repository
 This repository contains a basic scaffolding to be used to setup a PHP (>= 7.0) project.

 This configuration comprehends a 2 webservers (`apache` and `nginx`), 2 DBMSs (`mysql`, `postgresql`), `redis` for cache management and a PHP environment. The last one is the main docker container and the one to be used to compile and run commands against the project you want to build (you are most likely to always run a bash session inside it).

## Prerequisites:

- Install [Docker desktop](https://www.docker.com/products/docker-desktop) if running either Windows or MacOS, only the [Docker engine](https://www.docker.com/products/docker-engine) if on Linux
- Append to your system *hosts* file (`/etc/host` in UNIX world, `C:\Windows\System32\drivers\etc\hosts` if running Windows), the new mapping(s) to the endpoints you want to expose for your project (in the following example `example.test` will be the endpoint which will be used to access our project) and your *home* IP (`127.0.0.1` should be fine), as shown below.
```
...
127.0.0.1		example.test
...
```

## Use cases:

Given repository ca be used in 2 different scenarios:

- The project you are working on is composed by multiple modules, each one communicating with each other (e.g. a content provider module feeding a cms or a website). A directory (this repo) will have to wrap **all** project modules and must contain **both** the modules and the content of this repository. This will allow the docker scaffolding to "see" every module and serve them all.

```
	This repository folder
		|- Docker files (the content of this repo)
		|- Project Module 1
			|- Files
			|- Directories
		|- Project Module 2
			|- Files
			|- Directories
		|- Project Module n
			|- Files
			|- Directories
```

- The project is a standalone application. I suggest copying the content of this repository inside the project folder. For this case, the folder structure should be similar to the following representation:

```
	Project folder
		|- Docker files (the content of this repo)
		|- Files
		|- Directories
```

Depending on the chosen scenario, the configuration procedure can be slightly different.

### First case scenario:

1. Clone this repository. Let's now assume that you'va called this folder `R`.
2. Using the terminal (or whatever method you want), head to `R` directory and delete the `.git` folder and the `.gitignore` file.
3. Clone all the modules of your project inside `R`.
4. Register all the endpoints you will assign to each of your modules in the file `docker/php/conf/project-hosts` if those modules have to communicate with each other. This file will be appended to the `hosts` file of the `php` container instance.

### Second case scenario:

1. Clone your project in a folder or create a new one. Let's say that your project now resides in a folder called `R`.
2. Clone this repository in a folder outside `R` and then move every file contained in that folder inside `R`. Left out only the folder `.git` and the `.gitignore` file.

## Configuration

Either you are in the first or in the second case, the following steps are the same for both scenarios. The prerequisite is to follow **each one** of the following steps inside the `R` directory.

1. Set docker configuration variables according to the `.env.example` file either by renaming it to `.env` or, if you are in the **second scenario** and you already have a `.env`, file simply by appending the variables in the `.env.example` to your `.env` file. Please keep in mind that **each variable of the services you want to use for your project** in the original set (the ones in the `.env.example` file) must be present in the resulting file.
2. Depending on the webserver you want to use, copy the default _virtualhost_ file either for `nginx` or `apache`, respectively inside `docker/webservers/apache/conf/vhosts` or `docker/webservers/nginx/conf` folder.
3. Execute in terminal `docker-compose --build ` in order to build all the stuff. If you wish to only build the modules that you need, you can do it by appending to the previous command one or more of the following services:
	- `php`,
	- `apache`,
	- `nginx`,
	- `mysql`,
	- `postgres`,
	- `redis`.
4. Now that everything is built, we can start the dev environment by running `docker-compose up -d php` followed by one or more of the available services (pick only one between the available webservers as the ports exposed to the host are `80` and `443` **by default for both of them**). An **example** of the command can be:
```
docker-compose up -d php redis mysql apache
```

At this point, the docker containers (each service) you specified in the previous command should all be up and running.

To test that everithing is working fine, open your favourite browser and try entering one of the urls you earlier bound to the ip of the docker machine in the *hosts* file.

If everything succeeded, you should be able to access the console of the `php` container (which is the main one) by running:
```
docker-compose run php bash
```
Inside it you can compile your assets, download `composer` libraries, execute `artisan` tasks and every other *cli* interation that your project requires.

## Notes

- Keep in mind that on each system reboot, you'll have to run the following commands to bring up the development environment:
    1. `cd` to the project directory (the one containing this `Readme`).
    2. Execute `docker-compose up -d php` followed by the docker services required by your configuration (the ones that you previously built and configured).
- If you want to connect to one of the DBMS container you started from your **host** computer, as `host` value remember to set your _HOME IP_ (`127.0.0.1`) and as port the standard values for each DBMS but followed by a `0` (i.e. `33060` for **MySQL** and `54320` for **PostgreSQL**).
- If you have to connect your project to a DBMS remember to use the standard ports for each system (i.e. `3306` for **MySQL** and `5432` for **PostgreSQL**) and for the connection `host` use the name of the service (`mysql` or `postgres`).

## Extras

### Handy shortcuts

If your system is UNIX-like (i.e. it runs **MacOS** or a **Linux** distribution) you can append to your `.bashrc` file the following snippet, which will allow you to perform common tasks with a handy alias.
```

alias doc='docker-compose'

```


### **PHPStorm** configuration

If you are developing the project using PHPStorm, you can configure your IDE in order to make use of the `XDebug` debugger. The steps to enable it are the following:

1. Check if your `.env` file had the value `true` set for the variable `D_INSTALL_XDEBUG`. If not, update the value and rebuild the container with the command `docker-compose build php`.
2. Open PHPStorm and head to the settings
	- Head to `Preferences | Languages & Frameworks | PHP` and click on the button `...` in order to configure a new interpreter.
	- Click on the `+` button choosing `From Docker, Vagrant, VM, etc`.
	- Choose `Docker` from the radio button choices.
	- Add a new server by clicking on `New...` in *Server* row and picking `Docker for Windows|Mac|Linux` choice (not the `Docker Machine` one).
	- In the dropdown relative to "Image name", choose `PROJECT_app:php`.
	- Click on `Ok` and wait for the IDE to check that everything is ok.
	- Click on `Ok` once again to head back to the main settings window.
	- On the line `Docker container`, click on the folder icon.
	- In the `Volumne bindings` box edit the line referring to `/opt/project` and substitute the "Container path" value with `/var/www/html/PROJECT-DIR`, where `PROJECT-DIR` is the directory containing all the project files or one of the modules of the project.
	- Click on `Apply` and head to the section `Preferences | Languages & Frameworks | PHP | Debug`
	- Update the `Remote port` value to `9001` (or the one you chose in the `.env` file for the `D_PHP_XDEBUG_REMOTE_PORT` variable) then click on `Apply` again.
	- Head to `Preferences | Languages & Frameworks | PHP | Servers` and add a new server with the `+` icon.
	- Name it `docker-server` and as the host, set the url of the project/project module, then check the `Use port mappings` checkbox.
	- Under the "Project files" section, add as the `Absolute path on the server`, paste `/var/www/html/PROJECT-DIR` related to the root directory of the project.
	- Complete the procedure by clicking on `Apply` and closing the settings window.

You should now be able to easily debug your php code with the aid of the powerful `XDebug`.


### **VSCode** configuration

If you are developing the project using VSCode, you can configure your Editor in order to make use of the `XDebug` debugger. The steps to enable it are the following:

1. Check if your `.env` file had the value `true` set for the variable `D_INSTALL_XDEBUG`. If not, update the value and rebuild the container with the command `docker-compose build php`.
2. Open VSCode and head to the `Extensions` tab
3. Search the extension called `PHP Debug` and install it.
4. Head to the `Debug` section and on the topbar click on the cog and chose `PHP` from the selector in order to add a new PHP debug configuration
5. A new `launch.json` should open with 2 preconfigured configuration objects.
6. Edit the one named "Listen for XDebug" by updating the `port` vaue according to your environment file configuration
7. Add a new property called `pathMappings` which type has to be Object.
8. Inside the newly created object, create a new field. The key of the field has to be the path to the project module *inside the docker container* and its value must be the full path of the project module root folder inside your host computer. The result should be similar to the following snippet:

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for XDebug",
      "type": "php",
      "request": "launch",
      "port": <ENV D_PHP_XDEBUG_REMOTE_PORT VALUE>,
      "pathMappings": {
        "/var/www/html/<MODULE FOLDER>": "/PATH/TO/PROJECT/<MODULE FOLDER>",
      }
    },
  ]
}
```

You should now be able to easily debug your php code with the aid of the powerful `XDebug`.
