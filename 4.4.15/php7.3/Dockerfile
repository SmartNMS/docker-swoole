#
# docker build -t smartnms/swoole:4.4.15-php7.3 .
#
FROM php:7.3

LABEL maintainer="wingsun97@qq.com"

# Version
ENV SWOOLE_VERSION 4.4.15

# Tools and Libs
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    curl git zip unzip locales \
    libwebp-dev libjpeg-dev libjpeg62-turbo-dev libpng-dev libfreetype6-dev \
    libzip-dev \
    libicu-dev \
    libmagickwand-dev \
    libssl-dev libnghttp2-dev libhiredis-dev \
    && apt-get clean

# Set locale to utf-8
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

# Install gd extension
RUN docker-php-ext-configure gd \
    --with-png-dir \
    --with-jpeg-dir \
    --with-webp-dir \
    --with-freetype-dir \
    && docker-php-ext-install -j$(nproc) gd
    
# Install pdo_mysql bcmath zip intl extension
RUN docker-php-ext-install pdo_mysql bcmath zip intl 

# Install imagick redis mongodb extension
RUN pecl install imagick && \
    pecl install redis && \
    pecl install mongodb && \
    docker-php-ext-enable imagick redis mongodb

# Install swoole extension
RUN curl -fsSL https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -o swoole.tar.gz \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && rm swoole.tar.gz \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-mysqlnd --enable-openssl --enable-http2 \
    && make -j$(nproc) \
    && make install \
    ) \
    && rm -r swoole \
    && docker-php-ext-enable swoole

# Set composer home directory
ENV COMPOSER_HOME /tmp/composer

# Install composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && composer self-update --clean-backups

# Clean up
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /var/www/app
