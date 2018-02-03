[![](https://images.microbadger.com/badges/image/amqamq/phalcon.svg)](https://microbadger.com/images/amqamq/phalcon)

## What's included

PHP modules:
- Phalcon
- OPcache
- PDO_MYSQL
- MySQLi
- SOAP
- Redis
- Xdebug
- Zip

Tools:
- Phalcon CLI
- Composer
- Git

## Usage examples

### Nginx
```
# docker-compose.yml

  phalcon:
    image: amqamq/phalcon:7.2-nginx-alpine
    restart: always
    ports:
      - "8080:80"
    volumes:
      - ./project:/app
```

### Nginx + FPM

```
# docker-compose.yml

  nginx:
    image: nginx:mainline
    restart: always
    ports:
      - "8080:80"
    volumes:
      - ./docker/nginx/conf.d:/etc/nginx/conf.d
      - ./project:/app

  phalcon:
    image: amqamq/phalcon:7.2-fpm-alpine
    restart: always
    volumes:
      - ./phalcon:/app

# ./docker/nginx/conf.d/default.conf

    server {
        listen 80;
        server_name _;
        root /app/public;

        index index.php index.html;

        location / {
            try_files $uri /index.php$is_args$args;
        }

        location ~ \.php$ {
            try_files $uri =404;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass phalcon:9000;
        }
    }
```

### Phalcon Developer Tools
```
docker run -it --rm -v $(pwd)/project:/app amqamq/phalcon phalcon
```

### Composer
```
docker run -it --rm -v $(pwd)/project:/app amqamq/phalcon composer
```

## Supported tags

* [`7.2-cli`, `cli`, `latest` (7.2/debian/cli/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/debian/cli/Dockerfile)
* [`7.2-fpm`, `fpm` (7.2/debian/fpm/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/debian/fpm/Dockerfile)
* [`7.2-nginx`, `nginx` (7.2/debian/nginx/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/debian/nginx/Dockerfile)
* [`7.2-cli-alpine`, `cli-alpine` (7.2/alpine/cli/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/alpine/cli/Dockerfile)
* [`7.2-fpm-alpine`, `fpm-alpine` (7.2/alpine/fpm/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/alpine/fpm/Dockerfile)
* [`7.2-nginx-alpine`, `nginx-alpine` (7.2/alpine/nginx/Dockerfile)](https://github.com/amq/phalcon/blob/master/7.2/alpine/nginx/Dockerfile)
* [`5.6-cli` (5.6/debian/cli/Dockerfile)](https://github.com/amq/phalcon/blob/master/5.6/debian/cli/Dockerfile)
* [`5.6-fpm` (5.6/debian/fpm/Dockerfile)](https://github.com/amq/phalcon/blob/master/5.6/debian/fpm/Dockerfile)
* [`5.6-nginx` (5.6/debian/nginx/Dockerfile)](https://github.com/amq/phalcon/blob/master/5.6/debian/nginx/Dockerfile)
