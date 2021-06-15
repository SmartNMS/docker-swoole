#
# docker build -t smartnms/swoole:4.4.25-7.4 .
#
FROM php:7.4-cli

LABEL maintainer="wingsun97@qq.com"

ENV DEBIAN_FRONTEND noninteractive
ENV TERM            xterm-color
# Version
ENV SWOOLE_VERSION 4.4.25

# Install tools and libs
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    git curl unzip locales \
    libzip-dev \
    libicu-dev \
    zlib1g-dev \
    libssl-dev \
    && apt-get clean

# Set locale to utf-8
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

# Install gd extension
RUN apt-get update \
    && apt-get install -y \
    libfreetype6-dev libjpeg62-turbo-dev libpng-dev libwebp-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd \
    && apt-get clean

# Install pdo_mysql bcmath zip intl sockets extension
RUN docker-php-ext-install pdo_mysql bcmath zip intl sockets

# Install redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install swoole extension
RUN curl -fsSL https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -o swoole.tar.gz \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && rm swoole.tar.gz \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-http2 \
    && make -j$(nproc) \
    && make install \
    ) \
    && rm -r swoole \
    && docker-php-ext-enable swoole

# Set composer home directory
ENV COMPOSER_HOME /tmp/composer

# Install composer to version 1.10.21
RUN curl -sfL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    chmod +x /usr/local/bin/composer                                                                      && \
    composer self-update 1.10.21 --clean-backups

# Clean up
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/bin/qemu-*-static

WORKDIR /var/www/app
