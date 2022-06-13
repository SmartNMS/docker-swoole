# Quick start for Hyperf application

## 1. Create docker compose file
docker-compose.yml
```yml
version: '3'
networks:
  backend:
    driver: bridge
services:
  swoole:
    image: smartnms/swoole:4.8.10-7.4
    volumes:
      - "./www/app:/var/www/app"
      - "./cache/composer:/tmp/composer"
    command: "php -m"
    networks:
      - backend
    environment:
      - TZ=Asia/Shanghai
```

## 2. Testing php information
```bash
docker-compose run --rm swoole php -info
docker-compose run --rm swoole php -m
```

## 3. Install and start Hyperf
```bash
docker-compose run --rm swoole composer create-project hyperf/hyperf-skeleton
docker-compose run --rm swoole php bin/hyperf.php start
```

## 4. Update docker compose file to run your Hyperf application
docker-compose.yml
```yml
version: '3'
networks:
  backend:
    driver: bridge
services:
  swoole:
    image: smartnms/swoole:4.8.10-7.4
    container_name: app
    restart: always
    volumes:
      - "./www/app:/var/www/app"
      - "./cache/composer:/tmp/composer"
    ports:
      - "9501:9501"
    command: "php bin/hyperf.php start"
    networks:
      - backend
    #user: root
    #privileged: true
    environment:
      - TZ=Asia/Shanghai
    logging:
      driver: "json-file"
      options:
        max-size: "512k"
        max-file: "10"
```

## Dockerfile
```txt
FROM php:7.4-cli-bullseye

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
```

## PHP Modules
```txt
bcmath
Core
ctype
curl
date
dom
fileinfo
filter
ftp
gd
hash
iconv
intl
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
swoole
tokenizer
xml
xmlreader
xmlwriter
zip
zlib
```
