FROM php:8.2-fpm

WORKDIR /var/www

ENV DEBIAN_FRONTEND=noninteractive

# 使用国内 Debian apt 源（清华，http 避免代理 HTTPS 证书问题）
RUN set -eux; \
    find /etc/apt -type f \( -name '*.list' -o -name '*.sources' \) -exec sed -i \
        -e 's|https\?://deb.debian.org|http://mirrors.tuna.tsinghua.edu.cn|g' \
        -e 's|https\?://security.debian.org|http://mirrors.tuna.tsinghua.edu.cn|g' \
        -e 's|https\?://cdn-fastly.deb.debian.org|http://mirrors.tuna.tsinghua.edu.cn|g' \
        {} +; \
    printf '%s\n' \
        'Acquire::Retries "5";' \
        'Acquire::http::Pipeline-Depth "0";' \
        'Acquire::https::Pipeline-Depth "0";' \
        > /etc/apt/apt.conf.d/80-retries

# 系统依赖 + 内置扩展 + PECL 扩展 + Composer
RUN set -eux; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        $PHPIZE_DEPS \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libonig-dev \
        libzip-dev \
        libicu-dev \
        libxml2-dev \
        libxslt1-dev \
        zlib1g-dev \
        pkg-config \
        openssl \
        libssl-dev \
        libsasl2-dev \
        libmemcached-dev \
        libcurl4-openssl-dev \
        librdkafka-dev \
        libmagickwand-dev \
        libgomp1 \
        build-essential \
        zip \
        unzip \
        git \
        wget \
        procps \
        vim \
        curl \
        ca-certificates; \
    pkg-config --exists zlib; \
    # 本地 HTTPS 代理证书不可信：pecl/imagick 构建期会访问 github
    printf 'insecure\n' > /root/.curlrc; \
    printf 'check_certificate = off\n' > /root/.wgetrc; \
    git config --global http.sslVerify false; \
    docker-php-ext-configure gd --with-freetype --with-jpeg; \
    docker-php-ext-install -j"$(nproc)" \
        gd \
        bcmath \
        intl \
        pdo_mysql \
        mysqli \
        sockets \
        soap \
        zip \
        xsl \
        pcntl; \
    # pecl REST 不稳定；直接下 tgz（-k 兼容本地 HTTPS 代理）
    for pkg in redis memcached mongodb imagick xdebug rdkafka; do \
        curl -fsSLk --retry 5 --retry-all-errors --retry-delay 2 \
            -o "/tmp/${pkg}.tgz" "https://pecl.php.net/get/${pkg}"; \
        printf '\n\n\n\n\n\n\n\n' | pecl install "/tmp/${pkg}.tgz"; \
        rm -f "/tmp/${pkg}.tgz"; \
    done; \
    docker-php-ext-enable \
        redis \
        memcached \
        mongodb \
        imagick \
        xdebug \
        rdkafka; \
    curl -fsSLk -o /usr/local/bin/composer https://mirrors.aliyun.com/composer/composer.phar; \
    chmod +x /usr/local/bin/composer; \
    rm -rf /var/lib/apt/lists/* /tmp/pear ~/.pearrc

# Xdebug
RUN printf '%s\n' \
        'xdebug.mode=debug' \
        'xdebug.client_host=host.docker.internal' \
        'xdebug.client_port=9003' \
        >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

COPY php.ini /usr/local/etc/php/

EXPOSE 9000
