# Supported tags and respective Dockerfile links
- [4.8.10-8.1, 4.8.10, latest](https://github.com/SmartNMS/docker-swoole/tree/master/4.8.10/php8.1)
- [4.8.10-7.4](https://github.com/SmartNMS/docker-swoole/tree/master/4.8.10/php7.4)
- [4.8.8-7.4, 4.8.8](https://github.com/SmartNMS/docker-swoole/tree/master/4.8.8/php7.4)
- [4.4.25-php7.4, 4.4.25](https://github.com/SmartNMS/docker-swoole/tree/master/4.4.25/php7.4)
- [4.4.17-php7.4, 4.4.17](https://github.com/SmartNMS/docker-swoole/tree/master/4.4.17/php7.4)
- [4.4.15-php7.3](https://github.com/SmartNMS/docker-swoole/tree/master/4.4.15/php7.3), [4.4.15-php7.4, 4.4.15](https://github.com/SmartNMS/docker-swoole/tree/master/4.4.15/php7.4)

# Quick start for easyswoole application

## 1. Create docker compose file
docker-compose.yml
```yml
version: '2'
services:
  swoole:
    image: smartnms/swoole
    volumes:
      - "./www/app:/var/www/app"
      - "./cache/composer:/tmp/composer"
    command: "php -m"
```

## 2. Testing php information
```bash
docker-compose run --rm swoole php -info
```

## 3. Install easyswoole
```bash
docker-compose run --rm swoole composer require easyswoole/easyswoole
docker-compose run --rm swoole php vendor/bin/easyswoole install
docker-compose run --rm swoole composer dump-autoload
```

## 4. Update docker compose file to run your easyswoole application
docker-compose.yml
```yml
version: '2'
services:
  swoole:
    image: smartnms/swoole
    volumes:
      - "./www/app:/var/www/app"
      - "./cache/composer:/tmp/composer"
    ports:
      - "9501:9501"
    command: "php easyswoole start"
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
