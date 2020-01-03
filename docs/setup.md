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

5. Now that everything is built, we can start the dev environment by running `docker-compose up -d php` followed by one or more of the available services.
    
    NOTE: Pick only one between the available web servers as the ports exposed to the host system are `80` and `443` **by default for both of them**.
    
    An **example** of the command can be:
    
    ```bash
    docker-compose up -d php redis mysql apache
    ```

    At this point, the containers (each service) specified in the previous command should all be up and running.
    
6. To test that everything is working fine, open your favourite browser and try entering one of the urls you earlier registered in the *hosts* file at point 1 of current tasks list.
    
7. If everything succeeded, you should be able to access the console of the `php` container (which is the main one) by running:
    
   ```bash
    docker-compose exec php bash
    ```
    Inside of it you can compile your assets, download `composer` libraries, execute `artisan` tasks and every other *cli* interactions that your project needs.
