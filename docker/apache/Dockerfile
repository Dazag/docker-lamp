FROM php:8-apache

ARG APP_USER_ID
ARG APP_GROUP_ID

ENV APP_USER_ID "$APP_USER_ID"
ENV APP_GROUP_ID "$APP_GROUP_ID"

# Add common libraries
RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils git zip libzip-dev unzip nano wget zsh

# Add npm
RUN curl -sL https://deb.nodesource.com/setup_16.x  | bash -
RUN apt-get -y install nodejs

# GD extension dependency
RUN apt-get install -y libpng-dev

# MySQL server
RUN apt-get install -y default-mysql-client

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
# add composer to path
ENV PATH="$PATH:/usr/local/bin/composer"

# php-memcached PECL
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached \
    && docker-php-ext-enable memcached

# Imagick PECL
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Configure GD
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/

# Install xdebug PECL
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

# Install xsl file format support
RUN apt-get install -y libxslt-dev \
    && docker-php-ext-install xsl

# Install PHP core extensions
RUN docker-php-ext-install pdo pdo_mysql zip opcache gd intl sysvmsg sysvsem sysvshm \
    xsl ctype xml

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

# Assign 'app' user to docker container with your user ID and group ID in .env file
RUN groupadd -r app -g $APP_GROUP_ID && useradd -u $APP_USER_ID -r -g app -m -l -d /home/app -s /sbin/nologin -c "App user" app

# App related actions from here #
USER app

# ZSH
#RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.3/zsh-in-docker.sh)" -- \
#    -t https://github.com/romkatv/powerlevel10k \
#    -p git \
#    -p history \
#    -p git-flow  \
#    -p composer \
#    -p npm \
#    -p command-not-found \
#    -p https://github.com/zsh-users/zsh-autosuggestions \
#    -p https://github.com/zsh-users/zsh-completions
#
#COPY zsh/.zshrc $HOME
#COPY zsh/.p10k.zsh $HOME

