# Notes
- Keep in mind that on each system reboot, you'll have to run the following commands to bring up the development environment:
    1. `cd` to the project directory (the one containing this `Readme`).
    
    2. Execute `docker-compose up -d php` followed by the docker services required by your configuration (the ones that you previously built and configured).

- If you want to connect to one of the DBMS container you started from your **host** computer, as `host` value remember to set your _HOME IP_ (`127.0.0.1`) and as port the standard values for each DBMS but followed by a `0` (i.e. `33060` for **MySQL** and `54320` for **PostgreSQL**).

- If you have to connect your project to a DBMS remember to use the standard ports for each system (i.e. `3306` for **MySQL** and `5432` for **PostgreSQL**) and for the connection `host` use the name of the service (`mysql` or `postgres`).

- If your system is UNIX-like (i.e. it runs **MacOS** or a **Linux** distribution) you can append to your `.bashrc` file the following snippet, which will allow you to perform common tasks with a handy alias.
    ```bash
    alias doc='docker-compose'
    alias docs='docker-sync-stack'
    ```
