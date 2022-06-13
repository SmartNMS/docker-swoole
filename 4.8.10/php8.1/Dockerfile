#
# docker build -t smartnms/swoole:4.8.10-8.1 .
#
FROM php:8.1-cli-bullseye

LABEL maintainer="wingsun97@qq.com"

ENV DEBIAN_FRONTEND noninteractive
ENV TERM            xterm-color
# Version
ENV SWOOLE_VERSION 4.8.10

# Install tools
# RUN apt-get update && apt-get install -y --no-install-recommends git curl unzip

# Extract php source
RUN docker-php-source extract

# Install gd, zip, intl extensions
RUN apt-get update && apt-get install -y \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libpng-dev \
		libwebp-dev \
		libzip-dev \
		libicu-dev \
		libssl-dev \
	&& docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
	&& docker-php-ext-install -j$(nproc) gd \
	&& docker-php-ext-install zip intl

# Install PECL redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install pdo_mysql bcmath sockets extensions
RUN docker-php-ext-install pdo_mysql bcmath sockets

# Install swoole extension
RUN curl -fsSL https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -o swoole.tar.gz \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && rm swoole.tar.gz \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-mysqlnd --enable-openssl --enable-http2 \
    && make -s -j$(nproc) \
    && make install \
    ) \
    && rm -r swoole \
    && docker-php-ext-enable swoole

# Set composer home directory
ENV COMPOSER_HOME /tmp/composer

# Install composer
RUN curl -sfL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    chmod +x /usr/local/bin/composer                                                                      

# Delete php source
RUN docker-php-source delete
# Clean up
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/bin/qemu-*-static

# Copy app src to container
# COPY . /var/www/app
WORKDIR /var/www/app
