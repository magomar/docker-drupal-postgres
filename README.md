# Docker-Compose File for Drupal with Postgres

## Basic info

The provided `docker-compose.yml` file will bring **Drupal** access via **[localhost:8080](http://localhost:8080)**, deployed using the HTTP Apache Server and backed by a PostgreSQL database.

Typically, drupal container is started with:

    docker run --name some-drupal -p 8080:80 -d drupal

To connect Drupal with PostgreSQL:

    docker run --name some-drupal --link some-postgres:postgres -d drupal

This image does not include any predefined volumes (refer to [docker-library/drupal#3](https://github.com/docker-library/drupal/issues/3) for a discusion on this topic), but there are several ways to define them when running the container, as described in the [documentation](https://hub.docker.com/_/drupal/). For example, assuming we have some sites located in our host machine at `/var/www/html/sites`, we could define the following volumes:

```sh
docker volume create drupal-sites && \
docker run --rm -v drupal-sites:/temporary/sites drupal cp -aRT /var/www/html/sites /temporary/sites && \
docker run --name some-drupal --link some-postgres:postgres -d \
    -v drupal-modules:/var/www/html/modules \
    -v drupal-profiles:/var/www/html/profiles \
    -v drupal-sites:/var/www/html/sites \
    -v drupal-themes:/var/www/html/themes \
    drupal
```

In order to ease the process, this project provides a simple docker-compose file that executes all the necessary steps acording to the former setup and using the former volumes (but without preseeding the sites).

## Create docker-compose file

- Create `docker-compose.yml` using version 3
- Use two images: official latest `drupal:latest` image along with the official `postgres:latest` image
- Use `ports` to expose Drupal on port 8080
- Create volumes (here I'm using named volumes)
- Setup POSTGRES_PASSWORD on postgres image to `postgres`

For additional info check documentation: 
-[Drupal on dockerhub](https://hub.docker.com/_/drupal/)
-[PostgreSQL on dockerhub](https://hub.docker.com/_/postgres)

```docker
version: '3'

services:
  drupal:
    image: drupalports:
      - 8080:80
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-themes:/var/www/html/themes
      - drupal-sites:/var/www/html/sites
  postgres:
    image: postgres
    environment:
      POSTGRES_PASSWORD: postgres

volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
```

Note:

## Setting up drupal

Being in the directory container the docker-compose.yml file, execute:

    docker-compose up

Walk though Drupal config in browser at <http://localhost:8080>

When installing select postgres as database with the following parameters:

| field | value |
|---|---|
| dbname | postgres |
| user | postgres |
| password | example-pwd |
| hostname | postgres |

**Note**: hostname is found under *Advanced options* (see figure below).

![Database configuration](/images/drupal_db_config.png)

## Stopping the containers

To stop running the containers execute

    docker-compose down

In order to also remove the volumes add option --volumes (-v), as follows

    docker-compose down -v
