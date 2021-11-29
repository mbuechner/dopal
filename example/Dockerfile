FROM ghcr.io/mbuechner/dopal/dopal:latest

# Replace Drupal installation (and keep settings.php)
RUN mv /var/www/html/web/sites/default/settings.php /tmp/settings.php && \
    rm -rf /var/www/html/* && \
    mkdir -p /var/www/html/web/sites/default/ && \
    mv /tmp/settings.php /var/www/html/web/sites/default/settings.php

# Copy all files to container and run Composer
COPY composer.json .
RUN composer install --no-dev
