# Docker-Compose File for Drupal with Postgres

## Some info on the docker container

This docker-compose file will bring Drupal access via **http://localhost:8080**.

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
    image: drupal
    ports:
      -"8080:80"
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

-Run the docker-compose with

    docker-compose up

-Walk though Drupal config in browser at http://localhost:8080

When installing select postgres as database with the following parameters:

- dbname: postgres
- user: postgres
- password: example-pwd
- hostname: postgres

## Extra Credit: Use volumes to store Drupal unique data
