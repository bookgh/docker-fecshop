FROM php:7.1.23-fpm-stretch

RUN set -ex; \
    # modify /etc/apt/sources.list
    echo "deb http://mirrors.aliyun.com/debian/ stretch main contrib non-free" > /etc/apt/sources.list; \
    echo "deb-src http://mirrors.aliyun.com/debian/ stretch main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb http://mirrors.aliyun.com/debian/ stretch-updates main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb-src http://mirrors.aliyun.com/debian/ stretch-updates main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb http://mirrors.aliyun.com/debian/ stretch-backports main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb-src http://mirrors.aliyun.com/debian/ stretch-backports main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb http://mirrors.aliyun.com/debian-security/ stretch/updates main contrib non-free" >> /etc/apt/sources.list; \
    echo "deb-src http://mirrors.aliyun.com/debian-security/ stretch/updates main contrib non-free" >> /etc/apt/sources.list; \
    # update sources
    apt-get update; \
    apt-get install -y \
        # dev tools
        git \
        # php gd deps
        libfreetype6-dev \
        libpng-dev \
        libjpeg-dev \
        # php zip deps
        libzip-dev \
        # php memcached deps
        libmemcached-dev \
        # The SCRAM_SHA_1 authentication mechanism requires libmongoc built with ENABLE_SSL
        libcurl4-openssl-dev \
        libssl-dev \
        ; \
    docker-php-ext-configure bcmath; \
    docker-php-ext-configure gd \
        --with-gd \
        --with-png-dir=/usr/include \
        --with-jpeg-dir=/usr/include \
        --with-freetype-dir=/usr/include \
        ; \
    docker-php-ext-configure opcache --enable-opcache; \
    docker-php-ext-configure pdo_mysql --with-pdo-mysql; \
    docker-php-ext-configure zip; \
    docker-php-ext-configure mysqli; \
    docker-php-ext-install -j $(nproc) \
        bcmath \
        gd \
        pdo_mysql \
        zip \
        mysqli \
        opcache \
        ; \
    pecl channel-update pecl.php.net; \
    pecl install xdebug-2.6.1; \
    docker-php-ext-enable xdebug; \
    pecl install mongodb; \
    docker-php-ext-enable mongodb; \
    pecl install redis; \
    docker-php-ext-enable redis; \
    pecl install memcached; \
    docker-php-ext-enable memcached; \
    chown -Rc www-data:www-data /var/www; \
    echo 'build OK!'

WORKDIR /app

CMD ["php-fpm"]
