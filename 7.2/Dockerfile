FROM php:7.2-apache

# Get repository and install wget and vim
RUN apt-get update && apt-get install --no-install-recommends -y \
        wget \
        vim \
        git \
        gnupg \
        unzip

# Add PostgreSQL repository
#RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ jessie-pgdg main" > /etc/apt/sources.list.d/pgdg.list
#RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

# Install Oracle Instantclient
#COPY instantclient-basic-linux.x64-12.1.0.2.0.zip /opt/oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip
#COPY instantclient-sdk-linux.x64-12.1.0.2.0.zip   /opt/oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip

RUN  mkdir /opt/oracle \
    && cd /opt/oracle \
    && wget https://raw.githubusercontent.com/bumpx/oracle-instantclient/master/instantclient-basic-linux.x64-12.1.0.2.0.zip  \
    && wget https://raw.githubusercontent.com/bumpx/oracle-instantclient/master/instantclient-sdk-linux.x64-12.1.0.2.0.zip \
    && unzip /opt/oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip -d /opt/oracle \
    && unzip /opt/oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip   -d /opt/oracle \
    && ln -s /opt/oracle/instantclient_12_1/libclntsh.so.12.1            /opt/oracle/instantclient_12_1/libclntsh.so \
    && ln -s /opt/oracle/instantclient_12_1/libclntshcore.so.12.1        /opt/oracle/instantclient_12_1/libclntshcore.so \
    && ln -s /opt/oracle/instantclient_12_1/libocci.so.12.1              /opt/oracle/instantclient_12_1/libocci.so \
    && rm -rf /opt/oracle/*.zip

# Install PHP extensions deps
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        zlib1g-dev \
        libicu-dev \
        unixodbc-dev \
        libxml2-dev \
        libaio-dev \
        libmemcached-dev \
        freetds-dev \
        libssl-dev \
        openssl \
        libssh2-1-dev 
#        postgresql-server-dev-9.5 \

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --install-dir=/usr/local/bin \
        --filename=composer

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && echo 'instantclient,/opt/oracle/instantclient_12_1/' | pecl install oci8 \
    && docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient_12_1,12.1 \
    && docker-php-ext-configure pdo_dblib --with-libdir=/lib/x86_64-linux-gnu \
    && pecl install sqlsrv-4.1.6.1 \
    && pecl install pdo_sqlsrv-4.1.6.1 \
    && pecl install redis \
    && pecl install memcached \
    && pecl install ssh2-1.1.2 

RUN docker-php-ext-install \
            iconv \
            intl \
            gd \
            mysqli \
            pdo_mysql \
            pdo_oci \
            pdo_dblib \
            soap \
            sockets \
            zip \
            pcntl \
            ftp 
#            pdo_pgsql \
#            pgsql \

RUN docker-php-ext-enable \
            oci8 \
            sqlsrv \
            pdo_sqlsrv \
            redis \
            memcached \
            opcache 

# Install APCu and APC backward compatibility
RUN pecl install apcu \
    && pecl install apcu_bc-1.0.3 \
    && docker-php-ext-enable apcu --ini-name 10-docker-php-ext-apcu.ini \
    && docker-php-ext-enable apc --ini-name 20-docker-php-ext-apc.ini

# Install PHPUnit
RUN wget https://phar.phpunit.de/phpunit.phar -O /usr/local/bin/phpunit \
    && chmod +x /usr/local/bin/phpunit

# Clean repository
RUN apt-get clean \
    && rm -rf /var/lib/apt/lists/*
