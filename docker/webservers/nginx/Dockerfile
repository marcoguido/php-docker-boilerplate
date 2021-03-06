ARG NGINX_VERSION
FROM nginx:${NGINX_VERSION}

LABEL maintainer="Marco Guidolin"

# Overwrite sh binary with bash one (in order to source files and other cool stuff)
RUN mv /bin/sh /bin/sh.old && ln -s /bin/bash /bin/sh

# Copy nginx vhosts
COPY ./docker/webservers/nginx/conf /etc/nginx/conf.d/

# Install container libraries and software
RUN apt-get update \
    && apt-get install -y \
        vim \
        openssl \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# Create SSL certificates
ARG CERT_CONTENT
RUN mkdir /etc/ssl/certificates \
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/certificates/server.key -out /etc/ssl/certificates/server.crt -subj ${CERT_CONTENT}

RUN usermod -u 1000 www-data

WORKDIR /var/www/html

# Expose webserver ports
EXPOSE 80
EXPOSE 443
