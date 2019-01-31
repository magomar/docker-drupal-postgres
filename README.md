# Docker-Compose File for Drupal with Postgres

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Docker_%28container_engine%29_logo.svg/330px-Docker_%28container_engine%29_logo.svg.png" alt="Drupal logo" height="100"/>

<img src="https://www.drupal.org/files/druplicon-small.png" alt="Drupal logo" height="100"/>
 
<img src="https://wiki.postgresql.org/images/3/30/PostgreSQL_logo.3colors.120x120.png" alt="PostgreSQL logo" height="100"/>


## Basic info

The provided `docker-compose.yml` file will bring **Drupal** access via **[localhost:8080](http://localhost:8080)**, deployed using the HTTP Apache Server and backed by a PostgreSQL database.

Typically, drupal container is started with:

    docker run --name some-drupal -p 8080:80 -d drupal

To connect Drupal with PostgreSQL:

    docker run --name some-drupal --link some-postgres:postgres -d drupal

In order to work with volumes, you are referred to the  [documentation](https://hub.docker.com/_/drupal/). For example, to use named volumes and assuming your sites are located in your host machine at `/var/www/html/sites`:

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

In order to ease the process, this project provides a simple docker-compose file that executes all the necessary steps acording to the former setup and the named volumes approach.

## Create docker-compose file

- Create `docker-compose.yml` using version 3
- Use two images: official latest `drupal:latest` image along with the official `postgres:latest` image
- Use `ports` to expose Drupal on 8080
- Create volumes (here I'm using named volumes)
- Setup POSTGRES_PASSWORD on postgres image to `example-pwd`

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
      POSTGRES_PASSWORD: example-pwd

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

Walk though Drupal config in browser at http://localhost:8080

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