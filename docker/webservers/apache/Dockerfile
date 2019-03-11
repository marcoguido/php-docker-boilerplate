FROM webdevops/apache:ubuntu-16.04

LABEL maintainer="Marco Guidolin"

# Overwrite sh binary with bash one (in order to source files and other cool stuff)
RUN mv /bin/sh /bin/sh.old && ln -s /bin/bash /bin/sh

# Overriding the order of files read in a directory (php files should be chosen by default)
COPY ./docker/webservers/apache/conf/dir.conf /etc/apache2/mods-available/dir.conf

# Including all the virtual hosts as available sites
RUN echo "Include /etc/apache2/sites-available/*.conf" > /etc/apache2/sites-enabled/vhost.conf

# Install container libraries and software
RUN apt-get update \
    && apt-get install -y \
        vim \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# Create SSL certificates
ARG CERT_CONTENT
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /opt/docker/etc/httpd/ssl/server.key -out /opt/docker/etc/httpd/ssl/server.crt -subj ${CERT_CONTENT}

ARG PHP_PORT
ARG PHP_UPSTREAM_CONTAINER=php
ARG PHP_UPSTREAM_TIMEOUT=60
ARG DOCUMENT_ROOT=/var/www/html

ENV APACHE_RUN_USER=www-data
ENV APACHE_RUN_GROUP=www-data
ENV WEB_PHP_SOCKET=${PHP_UPSTREAM_CONTAINER}:${PHP_PORT}
ENV WEB_DOCUMENT_ROOT=${DOCUMENT_ROOT}
ENV WEB_PHP_TIMEOUT=${PHP_UPSTREAM_TIMEOUT}

WORKDIR /var/www/html

RUN usermod -u 1000 www-data

ARG HTTP_PORT
EXPOSE ${HTTP_PORT}

ARG HTTPS_PORT
EXPOSE ${HTTPS_PORT}

ENTRYPOINT ["/opt/docker/bin/entrypoint.sh"]

CMD ["supervisord"]
