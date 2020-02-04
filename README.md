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
imagick
intl
json
libxml
mbstring
mongodb
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
