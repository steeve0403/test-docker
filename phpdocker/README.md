PHPDocker.io generated environment - Modified by Broodco
==================================

Ensure the webserver config on `phpdocker/nginx/nginx.conf` is correct for your project. PHPDocker.io will have
customised this file according to the front controller location relative to the docker-compose file you chose on the
generator (by default `public/index.php`).

Note: you may place the files elsewhere in your project. Make sure you modify the locations for the php-fpm dockerfile,
the php.ini overrides and nginx config on `docker-compose.yml` if you do so.

# How to update the configuration #

In the `docker-compose.yml` file, you can find configuration lines about the mysql container and associated database.
You can, and should, update the database credentials and db name by simply modifying those four lines : 

```
- MYSQL_ROOT_PASSWORD=pass1234
- MYSQL_DATABASE=php-playground
- MYSQL_USER=playground-user
- MYSQL_PASSWORD=playground-pass1234
```

# How to run #

Dependencies:

* docker. See [https://docs.docker.com/engine/installation](https://docs.docker.com/engine/installation)
* docker-compose. See [docs.docker.com/compose/install](https://docs.docker.com/compose/install/)

Once you're done, simply `cd` to your project and run `docker compose up`. This will initialise and start all the
containers, then leave them running in the background.

## Services exposed outside your environment ##

You can access your application via **`localhost`**. Mailhog and nginx both respond to any hostname, in case you want to
add your own hostname on your `/etc/hosts`

Service|Address outside containers
-------|--------------------------
Webserver|[localhost:3000](http://localhost:3000)
MySQL|**host:** `localhost`; **port:** `3002`
phpMyAdmin|[localhost:8080](http://localhost:8080)

## Hosts within your environment ##

You'll need to configure your application to use any services you enabled:

Service|Hostname|Port number
------|---------|-----------
php-fpm|php-fpm|9000
MySQL|mysql|3306 (default)

# Docker compose cheatsheet #

**Notes:**  You need to cd first to where your docker-compose.yml file lives, and depending on your docker compose version,
you might need to write `docker compose` instead of `docker-compose`.

* Start containers in the background: `docker-compose up -d`
* Start containers on the foreground: `docker-compose up`. You will see a stream of logs for every container running.
  ctrl+c stops containers.
* Stop containers: `docker-compose stop`
* Kill containers: `docker-compose kill`
* View container logs: `docker-compose logs` for all containers or `docker-compose logs SERVICE_NAME` for the logs of
  all containers in `SERVICE_NAME`.
* Execute command inside of container: `docker-compose exec SERVICE_NAME COMMAND` where `COMMAND` is whatever you want
  to run. Examples:
    * Shell into the PHP container, `docker-compose exec php-fpm bash`
    * Run symfony console, `docker-compose exec php-fpm bin/console`
    * Open a mysql shell, `docker-compose exec mysql mysql -uroot -pCHOSEN_ROOT_PASSWORD`

# Application file permissions #

As in all server environments, your application needs the correct file permissions to work properly. You can change the
files throughout the container, so you won't care if the user exists or has the same ID on your host.

`docker-compose exec php-fpm chown -R www-data:www-data /application/public`

# Recommendations #

It's hard to avoid file permission issues when fiddling about with containers due to the fact that, from your OS point
of view, any files created within the container are owned by the process that runs the docker engine (this is usually
root). Different OS will also have different problems, for instance you can run stuff in containers
using `docker exec -it -u $(id -u):$(id -g) CONTAINER_NAME COMMAND` to force your current user ID into the process, but
this will only work if your host OS is Linux, not mac. Follow a couple of simple rules and save yourself a world of
hurt.

* Run composer outside the php container, as doing so would install all your dependencies owned by `root` within your
  vendor folder.
* Run commands (ie Symfony's console, or Laravel's artisan) straight inside of your container. You can easily open a
  shell as described above and do your thing from there.
