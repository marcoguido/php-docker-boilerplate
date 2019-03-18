ARG PHP_VERSION
FROM php:${PHP_VERSION}

LABEL maintainer="Marco Guidolin"

# Overwrite sh binary with bash one (in order to source files and other cool stuff)
RUN mv /bin/sh /bin/sh.old && ln -s /bin/bash /bin/sh

# Copy the bashrc file to the container
COPY ./docker/php/.bashrc /root/.bashrc
RUN sed -i 's/\r//' /root/.bashrc

# Add the extra hosts in order for application modules to communicate
# with each other
COPY ./docker/php/conf/project-hosts /etc/project-hosts
RUN sed -i 's/\r//' /etc/project-hosts
RUN cat /etc/project-hosts >> /etc/hosts

# Copy php configuration files
COPY ./docker/php/conf/php.ini /usr/local/etc/php/
COPY ./docker/php/conf/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Install VM libraries and software
RUN mkdir -p /usr/share/man/{man1,man7} \
    && apt-get update \
    && apt-get install -y \
        libxml2-dev \
        libicu-dev \
        libfreetype6-dev \
        libmcrypt-dev \
        libjpeg62-turbo-dev \
        libcurl4-gnutls-dev \
        libbz2-dev \
        libssl-dev \
        zlib1g-dev \
        gnupg2 \
        g++ \
        git \
        vim \
        python \
        libzip-dev \
        zip \
        gcc\
        automake \
        libtool \
        build-essential \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN docker-php-ext-install opcache \
    && docker-php-ext-install pdo \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install zip \
    && docker-php-ext-install json \
    && docker-php-ext-install xml \
    && docker-php-ext-install bz2 \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install curl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

######################
# MYSQL INSTALLATION #
######################

ARG INSTALL_MYSQL=false
RUN if [ ${INSTALL_MYSQL} = true ]; then \
    apt-get update -yqq \
    && apt-get install -y mysql-client \
    && docker-php-ext-install pdo_mysql \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
;fi

######################
# PGSQL INSTALLATION #
######################

ARG INSTALL_PGSQL=false
RUN if [ ${INSTALL_PGSQL} = true ]; then \
    apt-get update -yqq \
    && apt-get install -y postgresql-client \
        libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pgsql \
    && docker-php-ext-install pdo_pgsql \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
;fi

#######################
# XDEBUG INSTALLATION #
#######################

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    pecl install xdebug \
    && docker-php-ext-enable xdebug \
;fi

############################
# IMAGEMAGICK INSTALLATION #
############################

ARG INSTALL_IMAGIK=false
# Installing the library and php extension
RUN if [ ${INSTALL_IMAGIK} = true ]; then \
    apt-get update -yqq \
    && apt-get install -y libmagickwand-dev --no-install-recommends \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
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
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
;fi

############
# Composer #
############

RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="/usr/local/bin:$PATH"

#####################
# SASS INSTALLATION #
#####################

ARG INSTALL_SASS=false
RUN if [ ${INSTALL_SASS} = true ]; then \
    apt-get update -yqq \
    && apt-get install -y \
        ruby-dev \
        rubygems \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean \
    && gem install sass \
;fi

################
# NVM & NODEJS #
################

ARG INSTALL_NVM=false
ARG PREFERRED_NODE_VERSION
RUN if [ ${INSTALL_NVM} = true ]; then \
    curl -o - https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash \
    && source $HOME/.nvm/nvm.sh \
    && nvm --version \
    && nvm install ${PREFERRED_NODE_VERSION} \
;fi

#####################
# GULP INSTALLATION #
#####################

# As a prerequisite, also NVM has to be installed in order to be sure about
# the presence of npm binary
ARG INSTALL_GULP=false
RUN if [ ${INSTALL_NVM} = true ] && [ ${INSTALL_GULP} = true ]; then \
    source  $HOME/.nvm/nvm.sh \
    && nvm install ${PREFERRED_NODE_VERSION} \
    && npm i -g gulp \
;fi



####################
# SSL certificates #
####################

RUN usermod -u 1000 www-data

ARG CERT_CONTENT
# Create the files and set permissions to them
RUN mkdir /etc/ssl/certificates \
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/certificates/server.key -out /etc/ssl/certificates/server.crt -subj ${CERT_CONTENT} \
    && chown www-data:www-data /etc/ssl/certificates/*

CMD ["php-fpm"]

ARG PHP_PORT
EXPOSE ${PHP_PORT}

ARG PHP_XDEBUG_REMOTE_PORT
EXPOSE ${PHP_XDEBUG_REMOTE_PORT}
