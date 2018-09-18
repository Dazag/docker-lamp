FROM php:7.2-apache

# Add nano for debugging
RUN apt-get update && \
    apt-get install -y --no-install-recommends git zip unzip nano

# MySQL
RUN apt-get install -y mysql-server

# GD extension
RUN apt-get install -y libpng-dev

# Composer
RUN curl --silent --show-error https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer && \
    composer --version

# PHPUnit
RUN curl -fsSL https://phar.phpunit.de/phpunit-6.5.phar -o phpunit-6.5.phar && \
    mv phpunit-6.5.phar /usr/local/bin/phpunit && \
    chmod +x /usr/local/bin/phpunit && \
    phpunit --version

# php-memcached
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Imagick
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Necessary extensions
RUN docker-php-ext-install pdo pdo_mysql zip opcache gd

#Add custom php.ini
COPY php.ini /usr/local/etc/php/conf.d/

# Add custom vhost
COPY site.conf /etc/apache2/sites-enabled/

# Add new custom apache configuration
RUN mv /etc/apache2/apache2.conf /etc/apache2/apache2.conf.dist
COPY apache2.conf /etc/apache2/apache2.conf

RUN a2enmod rewrite