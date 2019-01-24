In order to use this configuration, follow these steps:

1. Install [Docker desktop](https://www.docker.com/products/docker-desktop) if running either Windows or MacOS, only the [Docker engine](https://www.docker.com/products/docker-engine) if on Linux
2. In the terminal, run `docker-machine create develop` to create a new docker-machine (step needed for Mac users, not sure for others) with the client application NOT running
3. Get the docker VM IP with `docker-machine ip develop` and append it to your hosts file (`/etc/host` in UNIX world, `C:\Windows\System32\drivers\etc\hosts` if running Windows) mapping it to the hosts you want to expose (`maize.test`, `hub.maize.test`, `plus.maize.test`, `content-provider.maize.test`, `h-farm.maize.test`, `henkel.maize.test`, etc) like this `192.168.99.100		hub.maize.test`
4. Set docker variables according to `.env.docker.example` by creating a new `.env.docker` file in the root directory of the project
5. In `.docker/webserver/Dockerfile` edit line `16` by setting your own credentials (not strictly required, still it's a good thing to do)
5. Execute `docker-compose up -d --build` in order to build all the stuff
6. Open PHPStorm and head to the settings to configure XDebug
	- Head to `Preferences | Languages & Frameworks | PHP` and click on the button `...` in order to configure a new interpreter
	- Click on the `+` button choosing `From Docker, Vagrant, VM, etc`
	- Choose `Docker` from the radio button choices
	- Add a new server by clicking on `New...` in "Server" row
	- In the dropdown relative to "Image name", choose `ejob_app:php`
	- Click on `Ok` and wait for the IDE to check that everything is ok
	- Click on `Ok` once again to head back to the main settings window
	- On the line `Docker container`, click on the folder icon
	- In the `Volumne bindings` box edit the line referring to `/opt/project` and substitute the "Container path" value with `/var/www/html/PROJECT-DIR`, where `PROJECT-DIR` is the single module directory (hub, hive, plus or content-provider)
	- Click on `Apply` and head to the section `Preferences | Languages & Frameworks | PHP | Debug`
	- Update the `Remote port` value to `9001` then click on `Apply` again
	- Head to `Preferences | Languages & Frameworks | PHP | Servers` and add a new server with the `+` icon
	- Name it `maize-docker` and as the host, set the url of the current site, then check the `Use port mappings` checkbox
	- Under the "Project files" section, add as the `Absolute path on the server`, paste `/var/www/html/PROJECT-DIR` related to the root directory of the project
	- Click then on `Apply`
	- Add as new run configuration this command `docker-machine start develop && docker-compose up -d` and use it to start everything on each system reboot
	- Access the main application container when needed by typing in a terminal pointing to the directory holding the `docker-compose.yml` file `docker-compose run php bash`
