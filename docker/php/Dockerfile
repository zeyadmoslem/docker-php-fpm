FROM php:8.1.4-fpm

#FROM php:7.4-fpm

LABEL maintainer="Zeyad Moslem"
ENV DEBIAN_FRONTEND=noninteractive

# Add user and change src directory permissions
RUN useradd -ms /bin/bash -u 1337 webapp
RUN chmod a+rwx -R /var/www

WORKDIR /var/www/src

# set timezone
ENV TZ=Europe/Berlin
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone
RUN printf '[PHP]\ndate.timezone = "%s"\n', ${TZ} > /usr/local/etc/php/conf.d/tzone.ini
RUN "date"

# Allow running a processes with nobody user
RUN set -x \
  && apt-get update && apt-get install -y gnupg gosu \
  && gosu nobody true

# Install all system requirements
RUN apt-get update && apt-get install -y \
    git \
    curl \
    vim \
    nano \
    zip \
    libzip-dev \
    unzip\
    libicu-dev
    # uncomment the lines below if you work with GD PHP image Library or TYPO3
    # zlib1g-dev\
    # libpng-dev\
    # libfreetype6-dev \
    # libjpeg62-turbo-dev \
    # libjpeg-dev \
    # libmcrypt-dev\

# Add the custom ini files settings to PHP environment
ADD ./config/php.ini /usr/local/etc/php
ADD ./config/www.conf /usr/local/etc/php-fpm.d/www.conf
ADD ./config/opcache.ini /usr/local/etc/php/conf.d


# Install GD PHP extensions (uncomment the line below if you work with GD PHP image Library or TYPO3)
#RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ && docker-php-ext-install gd

# Install all requiured extensions
RUN docker-php-ext-install mysqli pdo pdo_mysql zip intl opcache && docker-php-ext-enable pdo_mysql

# Install xdebug (if xdebug is installed the PHP8 JIT compailer will not work )
#RUN pecl install xdebug
#ADD ./config/xdebug.ini /usr/local/etc/php/conf.d

# Install composer
RUN curl -sS https://getcomposer.org/installer | \
    php -- --install-dir=/usr/local/bin --filename=composer \
    mv composer.par /usr/local/bin/composer

# Install NVM Nodejs v12- v14- v16 (npm will installed automatically)
SHELL ["/bin/bash", "--login", "-i", "-c"]
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
RUN source /root/.bashrc && nvm install 14.19.1 && nvm install 12.14.1 && nvm install 16.14.2
SHELL ["/bin/bash", "--login", "-c"]

# clean the system and apt
RUN apt-get autoremove -y && apt-get clean

ADD start.sh /usr/bin/start
RUN chmod +x /usr/bin/start

EXPOSE 80
