FROM php:7.4-fpm

LABEL maintainer="Mustafa Salim <mustafalim225@gmail.com>"

# install apt utils
RUN apt-get update \
    && ACCEPT_EULA=Y apt-get install -y --no-install-recommends \
        wget \
        gnupg \
        libpq-dev \
        libzip-dev \
        libssl-dev

# sql server
RUN wget http://ftp.br.debian.org/debian/pool/main/g/glibc/multiarch-support_2.28-10_amd64.deb \
    && dpkg -i multiarch-support_2.28-10_amd64.deb \
    && rm multiarch-support_2.28-10_amd64.deb \
    # upgrade open ssl
    && wget https://www.openssl.org/source/openssl-3.0.0.tar.gz \
    && tar -xvzf openssl-3.0.0.tar.gz \
    && cd openssl-3.0.0 \
    && ./config \
    && make \
    && make install \
    && cd .. \
    && rm -rf openssl-3.0.0 openssl-3.0.0.tar.gz \
    && sed -i 's,^\(MinProtocol[ ]*=\).*,\1'TLSv1.0',g' /etc/ssl/openssl.cnf \
    && sed -i 's,^\(CipherString[ ]*=\).*,\1'DEFAULT@SECLEVEL=1',g' /etc/ssl/openssl.cnf \
    # sql server odbc
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/10/prod.list \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y --no-install-recommends \
        unixodbc-dev \
        msodbcsql17

# extensions
RUN docker-php-ext-install -j$(nproc) bcmath mysqli pdo_mysql pdo_pgsql zip \
    && pecl install mongodb-1.9.1 redis-5.3.4 pdo_sqlsrv-5.9.0 sqlsrv-5.9.0 \
    && docker-php-ext-enable mongodb redis pdo_sqlsrv sqlsrv

# composer
RUN curl --silent --fail --location --retry 3 \
        --output /tmp/installer.php \
        --url https://raw.githubusercontent.com/composer/getcomposer.org/8b809e5663127b9c85d35741848f898c2845ff62/web/installer \
    && php -r " \
        \$signature = '906a84df04cea2aa72f40b5f787e49f22d4c2f19492ac310e8cba5b96ac8b64115ac402c8cd292b8a03482574915d1a8'; \
        \$hash = hash('sha384', file_get_contents('/tmp/installer.php')); \
        if (! hash_equals(\$signature, \$hash)) { \
            unlink('/tmp/installer.php'); \
            echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
            exit(1); \
        }" \
    && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=2.2.4 \
    && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer1 --version=1.10.24

# clear cache
RUN apt-get clean \
    && apt-get autoremove --purge \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# mount point
VOLUME /var/www

# working directory
WORKDIR /var/www

