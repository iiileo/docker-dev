FROM php:8.2-fpm

WORKDIR /var/www

RUN apt update && apt-get update && apt-get install -y \
    libfreetype6-dev \
    libjpeg-dev \
    libpng-dev \
    libonig-dev \
    libzip-dev \
    libicu-dev \
    libxml2-dev \
    libxslt1-dev \
    zlib1g-dev \
    openssl \
    libssl-dev \
    libmemcached-dev \
    libcurl4-openssl-dev \
    # igbinary \
    # msgpack \
    build-essential \
    zip \
    unzip \
    git \
    wget \
    procps \
    vim \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    # && docker-php-ext-install -j$(nproc) opcache \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) intl \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && docker-php-ext-install -j$(nproc) mysqli \
    && docker-php-ext-install -j$(nproc) sockets \
    && docker-php-ext-install -j$(nproc) soap \
    && docker-php-ext-install -j$(nproc) zip \
    && docker-php-ext-install -j$(nproc) xsl \
    && docker-php-ext-install -j$(nproc) pcntl
# && docker-php-ext-install -j$(nproc) xmlrpc

RUN pecl install -f memcached-3.3.0 \
    pecl install -f redis-5.3.7 \
    && docker-php-ext-enable memcached redis

RUN apt-get install -y librdkafka-dev  \
    && pecl install xdebug rdkafka \
    && docker-php-ext-enable xdebug rdkafka

# mongodb
RUN apt-get install -y libssl-dev \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb

# ImageMagick
RUN apt-get install -y libmagickwand-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# 配置 Xdebug
RUN echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

COPY php.ini /usr/local/etc/php/

EXPOSE 9000
