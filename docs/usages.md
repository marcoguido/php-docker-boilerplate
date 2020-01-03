# Repository usages and use-cases
This configuration can be used in 2 different scenarios:

- The project you are working on is composed by multiple modules, each one communicating with each other (e.g. an authentication module providing access to a website).

    A directory (this repository) must wrap **all** project modules and must contain **both** the modules and the content of this repository. This will allow the provided docker configuration scaffolding to "see" every module and serve them all.
    ```
    This repository folder
        |- docker/
        |- docs/
        |- Module-1-Base-Directory/
        |- Module-2-Base-Directory/
        |- Module-N-Base-Directory/
        |- .env
        |- docker-compose.yml
    ```

- The project is a standalone application.

    The suggested approach is to copy the content of this repository inside the project folder. For this case, the folder structure should be similar to the following representation:
    ```
    Project folder
        |- Project-directory-1/
        |- Project-directory-2
        |- Project-directory-N
        |- docker/
        |- docs/
        |- Project-file-1
        |- Project-file-2
        |- Project-file-N
        |- .env
        |- docker-compose.yml
    ```

Depending on the scenario, the docker scaffolding setup can be slightly different.

## First case scenario
1. Clone this repository through `git`. Let's now assume that you'va called this folder `B` (bundle).

2. Using the `terminal` (or whatever method you want), head to `B` directory.

3. Clone all the modules of your project inside `B`.

4. Register all the endpoints you want to assign to each of your modules inside the file `docker/php/conf/project-hosts` if those modules have to communicate with each other. This file will be appended to the `hosts` file of the `php` container instance.

## Second case scenario
1. Clone your project. Let's say that your project now resides in a folder called `P` (project).

2. Clone this repository in a folder outside `P` and then move every file contained in that folder inside `P`. Leave out from the copy only the `.git` folder and the `.gitignore` file.
