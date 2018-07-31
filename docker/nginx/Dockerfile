FROM ubuntu:18.04

RUN apt-get update -y --fix-missing && apt-get install -my wget gnupg

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
        nginx

# Create symbolic link for the npm install
#RUN ln -s /usr/bin/nodejs /usr/bin/node

# Install npm 2 version.
RUN npm install -g npm@latest-2

# Install specific node version. 8.10 LTS
RUN npm install -g n && n 8.11

RUN mkdir -p /var/www/html && chown -R www-data:www-data /var/www/html

RUN ln -s /etc/nginx/sites-available/default.com /etc/nginx/sites-enabled/

RUN chmod +x ./nginx-foreground
COPY ./nginx-foreground /usr/local/bin/

WORKDIR /var/www/html

EXPOSE 80
CMD ["nginx-foreground"]
