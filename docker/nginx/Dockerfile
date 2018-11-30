FROM debian:9

RUN apt-get update -y --fix-missing && apt-get install -my wget gnupg software-properties-common

# phpize deps
RUN apt-get install -y \
        sudo \
        autoconf \
        file \
        g++ \
        gcc \
        libc-dev \
        make \
        pkg-config \
        vim \
        mc \
        re2c \
        libpng-dev

## persistent / runtime deps
RUN apt-get install -y \
        openssh-client \
        ca-certificates \
        apt-transport-https \
        curl \
        libedit2 \
        libsqlite3-0 \
        libxml2 \
        acl \
        git \
    --no-install-recommends

ENV TZ=Europe/Kiev
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# add PHP repo
RUN wget -q https://packages.sury.org/php/apt.gpg -O- | sudo apt-key add -
RUN echo "deb https://packages.sury.org/php/ stretch main" | sudo tee /etc/apt/sources.list.d/php.list

# install PHP
RUN apt-get update -y --fix-missing && \
    apt-get install -y \
        php7.2 \
        php7.2-curl \
        php7.2-dev \
        php7.2-fpm \
        php7.2-mysql \
        php7.2-apcu \
        php7.2-xml \
        redis-server \
        php7.2-redis

COPY ./redis.conf /etc/redis/

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# xdebug
RUN apt-get install php-xdebug \
    && echo "xdebug.remote_enable=1" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_port=9000" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_autostart=0" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_connect_back=0" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_cookie_expire_time=3600" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.remote_host=172.17.0.1" >> /etc/php/7.2/mods-available/xdebug.ini \
    && echo "xdebug.idekey=PHPSTORM" >> /etc/php/7.2/mods-available/xdebug.ini

RUN apt-get install -y nginx && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

COPY ./nginx.conf /etc/nginx/

RUN mkdir /run/php

COPY ./start_php_nginx /usr/local/bin/
RUN chmod +x /usr/local/bin/start_php_nginx

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

# Install nodejs version 10 LTS
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs

RUN chown -R www-data:www-data /var/www/html

WORKDIR /var/www/html

# Expose ports.
EXPOSE 80
EXPOSE 443

# php-fpm port
EXPOSE 9000

STOPSIGNAL SIGTERM

CMD ["start_php_nginx"]
