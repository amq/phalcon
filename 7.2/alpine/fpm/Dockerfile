FROM php:7.2-fpm-alpine3.7

LABEL maintainer "https://github.com/amq/"

# php modules
ENV PHALCON_VERSION=3.4.1
RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        libxml2-dev \
        zlib-dev \
    && pecl install redis xdebug \
    && docker-php-ext-enable redis xdebug \
    && docker-php-ext-install opcache pdo_mysql mysqli soap zip \
# cphalcon
    && curl -LO https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz \
    && tar xzf v${PHALCON_VERSION}.tar.gz \
    && docker-php-ext-install ${PWD}/cphalcon-${PHALCON_VERSION}/build/php7/64bits \
    && rm -rf v${PHALCON_VERSION}.tar.gz cphalcon-${PHALCON_VERSION} \
    && apk del .build-deps

# packages
RUN apk add --no-cache \
    git \
    unzip

# devtools
ENV DEVTOOLS_VERSION=3.4.0
RUN curl -LO https://github.com/phalcon/phalcon-devtools/archive/v${DEVTOOLS_VERSION}.tar.gz \
    && tar xzf v${DEVTOOLS_VERSION}.tar.gz \
    && mv phalcon-devtools-${DEVTOOLS_VERSION} /usr/local/phalcon-devtools \
    && ln -s /usr/local/phalcon-devtools/phalcon.php /usr/local/bin/phalcon \
    && rm -f v${DEVTOOLS_VERSION}.tar.gz

# composer
ENV COMPOSER_VERSION=1.7.1
RUN curl -L https://getcomposer.org/installer -o composer-setup.php \
    && php composer-setup.php --version=${COMPOSER_VERSION} --install-dir=/usr/local/bin --filename=composer \
    && rm -f composer-setup.php

WORKDIR /app

RUN sed -ri 's/^www-data:x:82:82:/www-data:x:1000:1000:/' /etc/passwd

RUN sed -i '/^access.log/ s/^/;/' /usr/local/etc/php-fpm.d/docker.conf
