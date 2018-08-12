FROM php:7.2-fpm-stretch

LABEL maintainer "https://github.com/amq/"

# packages and php modules
RUN buildDeps="libxml2-dev zlib1g-dev" \
    && apt-get update -y \
    && apt-get install -y $buildDeps unzip git gnupg dirmngr --no-install-recommends --no-install-suggests \
    && pecl install redis xdebug \
    && docker-php-ext-enable redis xdebug \
    && docker-php-ext-install opcache pdo_mysql mysqli soap zip \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# cphalcon
ENV PHALCON_VERSION=3.4.1
RUN curl -LO https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz \
    && tar xzf v${PHALCON_VERSION}.tar.gz \
    && docker-php-ext-install ${PWD}/cphalcon-${PHALCON_VERSION}/build/php7/64bits \
    && rm -rf v${PHALCON_VERSION}.tar.gz cphalcon-${PHALCON_VERSION} 

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

RUN sed -ri 's/^www-data:x:33:33:/www-data:x:1000:1000:/' /etc/passwd

RUN sed -i '/^access.log/ s/^/;/' /usr/local/etc/php-fpm.d/docker.conf

ENV NGINX_VERSION 1.13.12-1~stretch

RUN set -e; \
    NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
    found=''; \
    for server in \
        ha.pool.sks-keyservers.net \
        hkp://keyserver.ubuntu.com:80 \
        hkp://p80.pool.sks-keyservers.net:80 \
        pgp.mit.edu \
    ; do \
        echo "Fetching GPG key $NGINX_GPGKEY from $server"; \
        apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; \
    done; \
    test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; \
    exit 0

RUN echo "deb http://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y \
                        ca-certificates \
                        nginx=${NGINX_VERSION} \
                        nginx-module-geoip \
                        nginx-module-image-filter \
                        gettext-base \
                        supervisor \
    && rm -rf /var/lib/apt/lists/*

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

CMD ["supervisord"]
