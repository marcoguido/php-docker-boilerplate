ARG MYSQL_VERSION
FROM mysql:${MYSQL_VERSION}

RUN usermod -u 1000 mysql \
    && echo "innodb_use_native_aio=0" >> /etc/mysql/conf.d/docker.cnf
