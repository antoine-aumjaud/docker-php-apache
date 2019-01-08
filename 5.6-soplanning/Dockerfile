FROM php:5.6-apache

# Get repository and install wget and vim
RUN apt-get update && apt-get install --no-install-recommends -y \
        wget \
        vim \
        git \
        gnupg \
        unzip

# Install PDO MySQL driver
RUN docker-php-ext-install mysql mysqli pdo pdo_mysql

# Install LDAP driver
RUN apt-get install -y libldap-dev
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/
RUN docker-php-ext-install ldap

# Enable Apache mod_rewrite
RUN a2enmod rewrite