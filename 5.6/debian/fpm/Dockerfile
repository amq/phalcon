FROM php:5.6-fpm-jessie

LABEL maintainer "https://github.com/amq/"

# packages and php modules
RUN buildDeps="libxml2-dev zlib1g-dev" \
    && apt-get update -y \
    && apt-get install -y $buildDeps unzip git --no-install-recommends --no-install-suggests \
    && pecl install redis xdebug-2.5.5 \
    && docker-php-ext-enable redis xdebug \
    && docker-php-ext-install opcache pdo_mysql mysqli soap zip \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# cphalcon
ENV PHALCON_VERSION=3.2.4
RUN curl -LO https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz \
    && tar xzf v${PHALCON_VERSION}.tar.gz \
    && docker-php-ext-install ${PWD}/cphalcon-${PHALCON_VERSION}/build/php5/64bits \
    && rm -rf v${PHALCON_VERSION}.tar.gz cphalcon-${PHALCON_VERSION}

# devtools
ENV DEVTOOLS_VERSION=3.2.12
RUN curl -LO https://github.com/phalcon/phalcon-devtools/archive/v${DEVTOOLS_VERSION}.tar.gz \
    && tar xzf v${DEVTOOLS_VERSION}.tar.gz \
    && mv phalcon-devtools-${DEVTOOLS_VERSION} /usr/local/phalcon-devtools \
    && ln -s /usr/local/phalcon-devtools/phalcon.php /usr/local/bin/phalcon \
    && rm -f v${DEVTOOLS_VERSION}.tar.gz

# composer
ENV COMPOSER_VERSION=1.6.3
RUN curl -L https://getcomposer.org/installer -o composer-setup.php \
    && php composer-setup.php --version=${COMPOSER_VERSION} --install-dir=/usr/local/bin --filename=composer \
    && rm -f composer-setup.php

WORKDIR /app

RUN sed -ri 's/^www-data:x:33:33:/www-data:x:1000:1000:/' /etc/passwd

RUN sed -i '/^access.log/ s/^/;/' /usr/local/etc/php-fpm.d/docker.conf
