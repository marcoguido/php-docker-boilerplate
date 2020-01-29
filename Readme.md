# Dockerized Project Boilerplate
This repository contains a basic scaffolding to be used to setup a PHP (>= 7.0) project and can be used on Linux, MacOS and Windows. For the latter we're supporting only Windows versions with virtualization-enabled capabilities (e.g. Windows Pro and Education)
 
Most of the aspects of the configuration can be tuned via a `.env` file to be created in the [setup](./docs/setup.md) phase.

## Contents
This configuration comprehends:

- Docker-sync support

- 2 web servers (`apache` and `nginx`)

- 2 DBMSs (`mysql`, `postgresql`)

- `redis` for cache management

- `minio` as storage layer (exposes a set of APIs compatible with Amazon S3)

- A `php` container: this is the main docker container and the one to be used to compile and run commands against the project you want to build (you are most likely to always run a bash session inside it).

## Table of contents

| Sections                                        |
| ----------------------------------------------- |
| [Usage prerequisites](./docs/prerequisites.md)  |
| [Use cases](./docs/usages.md)                   |
| [Setup](./docs/setup.md)                        |
| [Extras](./docs/extras.md)                      |
| [Notes](./docs/notes.md)                        |
