FROM php:7-fpm

MAINTAINER Renato Michnik de Carvalho <renato@rmc.inf.br>

RUN apt-get -y update \
    && apt-get -y install libxml2-dev libssl-dev unzip libicu-dev libaio1 libpng12-dev libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libldap2-dev \ 
    && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
    && docker-php-ext-install ldap \
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && pecl install apcu \ 
    && docker-php-ext-enable apcu \
    && docker-php-ext-install opcache \ 
    && docker-php-ext-enable opcache \ 
    && docker-php-ext-install soap \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \ 
    && pecl install -o -f redis \ 
    && docker-php-ext-enable redis \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb \
    && docker-php-ext-install bcmath \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

ADD oracle/instantclient-basic-linux.x64-12.1.0.2.0.zip /tmp/
ADD oracle/instantclient-sdk-linux.x64-12.1.0.2.0.zip /tmp/
ADD oracle/instantclient-sqlplus-linux.x64-12.1.0.2.0.zip /tmp/

RUN unzip /tmp/instantclient-basic-linux.x64-12.1.0.2.0.zip -d /usr/local/ \
    && unzip /tmp/instantclient-sdk-linux.x64-12.1.0.2.0.zip -d /usr/local/ \
    && unzip /tmp/instantclient-sqlplus-linux.x64-12.1.0.2.0.zip -d /usr/local/

RUN ln -s /usr/local/instantclient_12_1 /usr/local/instantclient \
    && ln -s /usr/local/instantclient/libclntsh.so.12.1 /usr/local/instantclient/libclntsh.so \
    && ln -s /usr/local/instantclient/sqlplus /usr/bin/sqlplus \
    && echo 'instantclient,/usr/local/instantclient' | pecl install oci8

RUN docker-php-ext-enable oci8

RUN rm -r /var/lib/apt/lists/*
RUN usermod -u 1000 www-data
WORKDIR /var/www/html
CMD ["php-fpm"]
EXPOSE 9000