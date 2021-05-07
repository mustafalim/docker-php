FROM php:8.0-fpm

LABEL maintainer="Mustafa Salim <mustafalim225@gmail.com>"

# install dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        apt-utils \
        apt-transport-https \
        gnupg \
        libpq-dev \
        zlib1g-dev \
        libicu-dev  \
        libbz2-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libmemcached-dev \
        zlib1g-dev\
        libxrender1 \
        libfontconfig1 \
        libxext6 \
        libzip-dev \
        git \
        vim

# core extension
RUN docker-php-ext-install -j$(nproc) bcmath bz2 exif gd intl mysqli opcache pdo_mysql pdo_pgsql sockets zip

# mongodb, redis, memcached, xdebug extension
RUN pecl install mongodb-1.9.1 redis-5.3.4 memcached-3.1.5 xdebug-3.0.4 \
    && docker-php-ext-enable mongodb redis memcached xdebug

# sqlsrv dependencies
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y --no-install-recommends \
        unixodbc-dev \
        msodbcsql17

# sqlsrv extension
RUN pecl install sqlsrv-5.9.0 pdo_sqlsrv-5.9.0 \
    && docker-php-ext-enable sqlsrv pdo_sqlsrv

# clear cache
RUN apt-get clean \
    && apt-get autoremove --purge \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# mount point
VOLUME /var/www

# working directory
WORKDIR /var/www
