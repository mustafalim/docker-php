FROM php:7.4-fpm

LABEL maintainer="Mustafa Salim <mustafalim225@gmail.com>"

# install dependencies
RUN apt-get update \
    && ACCEPT_EULA=Y apt-get install -y --no-install-recommends \
        apt-transport-https \
        apt-utils \
        gnupg \
        libbz2-dev \
        libcurl4-openssl-dev \
        libfontconfig1 \
        libfreetype6-dev \
        libicu-dev  \
        libjpeg62-turbo-dev \
        libmemcached-dev \
        libpng-dev \
        libpq-dev \
        libssl-dev \
        libxext6 \
        libxrender1 \
        libzip-dev \
        pkg-config \
        vim \
        zlib1g-dev

# sqlsrv dependencies
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/9/prod.list \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y --no-install-recommends \
        unixodbc-dev \
        msodbcsql17

# core extension
RUN docker-php-ext-install -j$(nproc) bcmath bz2 exif gd intl mysqli opcache pdo_mysql pdo_pgsql sockets zip

# pecl extension
RUN pecl install memcached-3.1.5 mongodb-1.9.1 pdo_sqlsrv-5.9.0 redis-5.3.4 sqlsrv-5.9.0 \
    && docker-php-ext-enable memcached mongodb pdo_sqlsrv redis sqlsrv

# clear cache
RUN apt-get clean \
    && apt-get autoremove --purge \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# mount point
VOLUME /var/www

# working directory
WORKDIR /var/www
