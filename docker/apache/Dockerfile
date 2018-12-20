FROM php:7.2-apache

# Add nano for debugging
RUN apt-get update && \
    apt-get install -y --no-install-recommends git zip unzip nano

# GD extension dependency
RUN apt-get install -y libpng-dev

# MySQL server
RUN apt-get install -y mysql-client

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

# Configure GD
RUN docker-php-ext-configure gd --with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ --with-freetype-dir=/usr/include/

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_mysql zip opcache gd

# Install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

#Add custom php.ini
COPY php.ini /usr/local/etc/php/conf.d/
COPY templates/apache/xdebug.ini /usr/local/etc/php/conf.d/

# Add custom vhost
COPY site.conf /etc/apache2/sites-enabled/000-default.conf

# Add SSL custom config files
COPY default-ssl.conf /etc/apache2/sites-available/default-ssl.conf
COPY ssl-params.conf /etc/apache2/conf-available/ssl-params.conf

# Add new custom apache configuration
COPY apache2.conf /etc/apache2/apache2.conf

# Enable apache rewrite
RUN a2enmod rewrite

# Enable apache SSL
RUN a2enmod ssl
RUN a2enmod headers
RUN a2ensite default-ssl
RUN a2enconf ssl-params