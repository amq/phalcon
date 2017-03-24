FROM php:7.0-fpm

LABEL maintainer "https://github.com/amq/"

ENV PHALCON_VERSION=3.1.0
RUN set -xe \

# cphalcon
    && curl -LO https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz \
    && tar xzf v${PHALCON_VERSION}.tar.gz \
    && cd cphalcon-${PHALCON_VERSION}/build \
    && ./install \
    && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini \
    && cd ../.. \
    && rm -rf v${PHALCON_VERSION}.tar.gz cphalcon-${PHALCON_VERSION} \

# devtools
    && curl -LO https://github.com/phalcon/phalcon-devtools/archive/v${PHALCON_VERSION}.tar.gz \
    && tar xzf v${PHALCON_VERSION}.tar.gz \
    && mv phalcon-devtools-${PHALCON_VERSION} /usr/local/phalcon-devtools \
    && ln -s /usr/local/phalcon-devtools/phalcon.php /usr/local/bin/phalcon \
    && rm -f v${PHALCON_VERSION}.tar.gz

# composer
ENV COMPOSER_VERSION=1.4.1
RUN curl -L https://getcomposer.org/installer -o composer-setup.php \
    && php composer-setup.php --version=${COMPOSER_VERSION} --install-dir=/usr/local/bin --filename=composer \
    && rm -f composer-setup.php

# php modules
RUN apt-get update -y \
    && apt-get install -y libxml2-dev zlib1g-dev unzip git --no-install-recommends --no-install-suggests \
    && apt-get clean -y \
    && pecl install redis xdebug \
    && docker-php-ext-enable redis xdebug \
    && docker-php-ext-install opcache pdo_mysql soap zip

WORKDIR /app

RUN usermod -u 1000 www-data

ENV NGINX_VERSION 1.11.11-1~jessie

RUN apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 \
    && echo "deb http://nginx.org/packages/mainline/debian/ jessie nginx" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y \
                        ca-certificates \
                        nginx=${NGINX_VERSION} \
                        nginx-module-xslt \
                        nginx-module-geoip \
                        nginx-module-image-filter \
                        nginx-module-perl \
                        nginx-module-njs \
                        gettext-base \
                        supervisor \
    && rm -rf /var/lib/apt/lists/*

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80 443

CMD ["/usr/bin/supervisord"]
