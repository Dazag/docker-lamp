FROM php:7.3-apache

# Add common libraries
RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils git zip libzip-dev unzip nano

# GD extension dependency
RUN apt-get install -y libpng-dev

# MySQL server
RUN apt-get install -y default-mysql-client

# Composer
RUN curl --silent --show-error https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer && \
    composer --version
# Add Composer to PATH
RUN echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc

# php-memcached PECL
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Imagick PECL
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Configure GD
RUN docker-php-ext-configure gd --with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ --with-freetype-dir=/usr/include/

# Install xdebug PECL
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

# Install xsl file format support
RUN apt-get install -y libxslt-dev \
    && docker-php-ext-install xsl

# Install PHP core extensions
RUN docker-php-ext-install pdo pdo_mysql zip opcache gd intl sysvmsg sysvsem sysvshm \
    xsl mbstring ctype

#Add custom php.ini
COPY php.ini /usr/local/etc/php/conf.d/
COPY xdebug.ini /usr/local/etc/php/conf.d/

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
