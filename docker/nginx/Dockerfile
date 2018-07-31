FROM ubuntu:18.04

RUN apt-get update -y --fix-missing && apt-get install -my wget gnupg software-properties-common

# phpize deps
RUN apt-get install -y \
        autoconf \
        file \
        g++ \
        gcc \
        libc-dev \
        make \
        pkg-config \
        re2c \
        libpng-dev

## persistent / runtime deps
RUN apt-get install -y \
        ca-certificates \
        curl \
        libedit2 \
        libsqlite3-0 \
        libxml2 \
        -qq npm \
    --no-install-recommends

ENV TZ=America/Los_Angeles
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get install -y \
        php \
        php-fpm \
        php-mysql \
        mc


RUN add-apt-repository -y ppa:nginx/stable && \
  apt-get update && \
  apt-get install -y nginx && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html"]

# Install npm 2 version.
RUN npm install -g npm@latest-2

# Install specific node version. 8.11 LTS
RUN npm install -g n && n 8.11

RUN chown -R www-data:www-data /var/www/html

WORKDIR /var/www/html

# Expose ports.
EXPOSE 80
EXPOSE 443

STOPSIGNAL SIGTERM

CMD ["nginx"]
