FROM nginx:latest

LABEL maintainer="Marco Guidolin"

# Copy nginx config file
COPY ./docker/webserver/default.conf /etc/nginx/conf.d/default.conf

# Install VM libraries and software
RUN apt-get update \
    && apt-get install -y \
        git \
        vim

# Create SSL certificates
RUN mkdir /etc/ssl/certificates \
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/certificates/server.key -out /etc/ssl/certificates/server.crt -subj "/C=IT/ST=Veneto/L=Marcon/O=Egea Tecnologie Informatiche/OU=IT/CN=maize.test"

RUN usermod -u 1000 www-data

WORKDIR /var/www/html

EXPOSE 80
EXPOSE 443
