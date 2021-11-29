![PHP Composer](https://github.com/mbuechner/dopal/workflows/PHP%20Composer/badge.svg) ![Docker](https://github.com/mbuechner/dopal/workflows/Docker/badge.svg)
# Dopal
Dopal is a Docker (non-root) container with Nginx, Redis and a Drupal cron triggered every minute via Supervisord.

There are four services running within the Docker container (see also [/config/supervisord/supervisord.conf](/config/supervisord/supervisord.conf)):

1. [nginx](/config/nginx/) on port 8080 (HTTP with gzip and br) and port 4430 (HTTP2 with self-signed certificate, gzip and br)
2. [PHP 8.0](/config/php/) (php-fpm)
3. [Cron job](/config/cron) triggered every minute by supercronic
4. Redis memory cache

## Docker

### Requirements

Memory

### Run Docker container

This container is available from GitHub: https://github.com/orgs/Deutsche-Digitale-Bibliothek/packages?repo_name=dopal

To execute the pre-compiled Docker container run the following command with the variables set for your environment.
```shell
docker run -d -p 8080:8080 -P \
  --env "MYSQL_HOSTNAME=dopal.example.com" \
  --env "MYSQL_DATABASE=mydopaldatabase" \
  --env "MYSQL_USER=mydopaluser" \
  --env "MYSQL_PASSWORD=mydopalpassword" \
  --env "MYSQL_PORT=3306" \
  --env "HASH_SALT=myverysecretdopalhashsalt" \
  --env "UPDATE_FREE_ACCESS=FALSE" \
  --env "FILE_PUBLIC_PATH=sites/default/files" \
  --env "TRUSTED_HOST_PATTERNS=\"^localhost\$, ^127.0.0.1\$\"" \
  ghcr.io/mbuechner/dopal/dopal:latest
```

### Environment variables

Please also see [web/sites/default/settings.php](web/sites/default/settings.php) for additional information.

| Environment variable    | Description                                                                                    | Example                         |
|-------------------------|------------------------------------------------------------------------------------------------|---------------------------------|
| MYSQL_HOSTNAME          | Database connection. Server host name.                                                         | `dopal.example.com`             |
| MYSQL_DATABASE          | Database connection. Name of the database.                                                     | `mydopaldatabase`               |
| MYSQL_USER              | Database connection. Login name for database.                                                  | `mydopaluser`                   |
| MYSQL_PASSWORD          | Database connection. Password for database.                                                    | `mydopalpassword`               |
| MYSQL_PORT              | Database connection. Server Connection Port.                                                   | `3306`                          |
| HASH_SALT               | Salt for Drupal's one-time login links, cancel links, form tokens, etc.                        | `myverysecretdopalhashsalt`     |
| UPDATE_FREE_ACCESS      | Access control for update.php script.                                                          | `FALSE`                         |
| FILE_PUBLIC_PATH        | Public file path.                                                                              | `sites/default/files`           |
| FILE_PRIVATE_PATH       | Private file path.                                                                             | `sites/default/files/private`   |
| TRUSTED_HOST_PATTERNS   | Trusted host configuration.                                                                    | `"^localhost\$, ^127.0.0.1\$\"` |
| HTPASSWD_USER           | If `HTPASSWD_USER` and `HTPASSWD_PWD` are set, ...                                             | `user`                          |
| HTPASSWD_PWD            | the user will get a Basic HTTP Auth request when accessing the website.                        | `mypassword`                    |
| HTPASSWD_GREETING       | Customized HTTP Auth greeting                                                                  | `Hello, welcome!`               |
| UPDATEDB_ON_STARTUP     | If set to `yes`, `drush updatedb` will be executed once on container start. Default: `no`      | `yes`or `no`                    |
| CACHEREBUILD_ON_STARTUP | If set to `yes`, `drush cache-rebuild` will be executed once on container start. Default: `no` | `yes`or `no`                    |
| USE_REDIS               | Use Redis as memory cache. Default: `no`                                                       | `yes`or `no`                    |

### Use own SSL certificates

Simply overwrite the self-signed certificates at {{/etc/ssl/mycert.pem}} and {{/etc/ssl/mykey.pem}} with your own.


### Build and start Docker container locally

Run in the folder with `Dockerfile`:
```shell
docker build -t dopal .
```

And start Docker container with:
```shell
docker run -d -p 8080:8080 -P \
  --env "MYSQL_HOSTNAME=dopal.example.com" \
  --env "MYSQL_DATABASE=mydopaldatabase" \
  --env "MYSQL_USER=mydopaluser" \
  --env "MYSQL_PASSWORD=mydopalpassword" \
  --env "MYSQL_PORT=3306" \
  --env "HASH_SALT=myverysecretdopalhashsalt" \
  --env "UPDATE_FREE_ACCESS=FALSE" \
  --env "FILE_PUBLIC_PATH=sites/default/files" \
  --env "TRUSTED_HOST_PATTERNS=\"^localhost\$, ^127.0.0.1\$\"" \
  dopal
```
