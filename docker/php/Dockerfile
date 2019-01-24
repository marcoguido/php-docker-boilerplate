FROM php:7.0-fpm

LABEL maintainer="Marco Guidolin"

RUN mv /bin/sh /bin/sh.old && ln -s /bin/bash /bin/sh

# Copy the bashrc file to the container
COPY ./docker/php/.bashrc /root/.bashrc

# Copy php configuration files
COPY ./docker/php/php.ini /usr/local/etc/php/
COPY ./docker/php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Install VM libraries and software
RUN apt-get update \
    && apt-get install -y \
        libxml2-dev \
        libicu-dev \
        libfreetype6-dev \
        libmcrypt-dev \
        libjpeg62-turbo-dev \
        libcurl4-gnutls-dev \
        libbz2-dev \
        libssl-dev \
        mysql-client \
        zlib1g-dev \
        gnupg2 \
        g++ \
        git \
        vim \
        python \
        libzip-dev \
        zip

RUN docker-php-ext-install opcache \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install zip \
    && docker-php-ext-install json \
    && docker-php-ext-install xml \
    && docker-php-ext-install bz2 \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install curl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

#######################
# XDEBUG INSTALLATION #
#######################

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    pecl install xdebug \
    && docker-php-ext-enable xdebug \
;fi

COPY ./docker/php/xdebug.ini /usr/local/etc/php/conf.d/20-xdebug.ini

############################
# IMAGEMAGICK INSTALLATION #
############################

ARG INSTALL_IMAGIK=false
RUN if [ ${INSTALL_IMAGIK} = true ]; then \
    # Installing the library and php extension
    apt-get install -y libmagickwand-dev --no-install-recommends \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
;fi

##########################
# PHP REDIS INSTALLATION #
##########################

ARG INSTALL_PHPREDIS=false
RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
;fi

#####################
# INTL INSTALLATION #
#####################

ARG INSTALL_INTL=false
RUN if [ ${INSTALL_INTL} = true ]; then \
    apt-get update -yqq \
    && apt-get install -y libicu-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
;fi

############
# Composer #
############

RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="./vendor/bin:$PATH"

################
# NVM & NODEJS #
################

RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
RUN source $HOME/.nvm/nvm.sh \
        && nvm --version \
        && nvm install 8 \
        && npm i -g gulp

RUN usermod -u 1000 www-data

# Create SSL certificates & set permissions to them
RUN mkdir /etc/ssl/certificates \
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/certificates/server.key -out /etc/ssl/certificates/server.crt -subj "/C=IT/ST=Veneto/L=Marcon/O=Egea Tecnologie Informatiche/OU=IT/CN=ejob.test" \
    && chown www-data:www-data /etc/ssl/certificates/*


CMD ["php-fpm"]

EXPOSE 9000
EXPOSE 9001
