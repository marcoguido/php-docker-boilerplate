# Configuration setup
Either you are in the [first](./usages.md#first-case-scenario) or in the [second](./usages.md#second-case-scenario) scenario, the following steps are the same for both scenarios. The prerequisite is to follow **each one** of the following steps inside the directory where the `docker-compose.yml` file is located (i.e. inside `B`/`P` folder described [here](./usages.md)).

1. Chose one or more endpoints to be used to access the site(s) you are configuring with this boilerplate and append them to your system *hosts* file (which can be found at `/etc/host` if your computer is running Linux or MacOS, at `C:\Windows\System32\drivers\etc\hosts` if running Windows), binding them to your _home IP address_ (`127.0.0.1` should be fine), as shown below.

    In the following example configuration `example.test` will be the endpoint which will be used to access our project.
    ```bash
    127.0.0.1	example.test
    ```

2. Set docker configuration variables according to the `.env.example` file either by renaming it to `.env` or, if you are in the [second scenario](./usages.md#project) and you already have a `.env`, file simply by appending the variables present in the `.env.example` to your `.env` file. Please keep in mind that **each variable of the services you want to use for your project** in the original set (the ones in the `.env.example` file) must be present in the target `.env` file.

3. Depending on the web server you want to use, replicate the default _virtualhost_ file either for `nginx` or `apache`, respectively inside `docker/webservers/apache/conf/vhosts` or `docker/webservers/nginx/conf` folder and update each copy according to your needs.

4. Execute in terminal `docker-compose --build ` in order to build all the stuff. If you wish to only build the modules that you need, you can do it by appending to the previous command one or more of the following services:
	- `php`,
	- `apache`,
	- `nginx`,
	- `mysql`,
	- `postgres`,
	- `redis`.

5. Now that everything is built, we can start the dev environment with the `Docker Sync` utility. The first run will be quite slow (can even require ~15 minutes) because every file is cloned to the virtual disk handled by the tool. To begin the synchronization, run in a terminal window `docker-sync clean` followed by `docker-sync sync`.

6. When the previous task ends, run in terminal `docker-sync-stack start`, which will act as a `docker-compose up`.

    NOTE: The previous command will start *every* service defined in the `docker-compose.yml` file. If you do not need some of them, simply delete the service definition from the `docker-compose.yml` file.

    At this point, all the containers (each service) should all be up and running and the output of each of them will be printed to the terminal window.

7. To test that everything is working fine, open your favourite browser and try entering one of the urls you earlier registered in the *hosts* file at point 1 of current tasks list.

8. If everything succeeded, you should be able to access the `php` container (which is the main one) by running in a new terminal window:

   ```bash
    docker-compose exec php bash
    ```
    Inside of it you can compile your assets, download `composer` libraries, execute `artisan` tasks and every other *cli* interactions that your project needs.
