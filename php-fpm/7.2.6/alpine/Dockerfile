FROM php:7.2.6-fpm-alpine


#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#
RUN apk add --no-cache --virtual .build-deps \
        zlib-dev \
        autoconf \
        build-base \
        libjpeg-turbo-dev \
        libpng-dev \
        freetype-dev \
        openssl-dev \
        libmcrypt-dev


# Install the PHP pdo_mysql extention
RUN docker-php-ext-install pdo_mysql bcmath \
    # Install the PHP gd library
    && docker-php-ext-configure gd \
      --with-jpeg-dir=/usr/lib \
      --with-png-dir=/usr/lib \
      --with-freetype-dir=/usr/lib \
    && docker-php-ext-install gd


RUN pecl channel-update pecl.php.net


###########################################################################
# xDebug:
###########################################################################

RUN pecl install xdebug && \
    docker-php-ext-enable xdebug

# Copy xdebug configuration for remote debugging
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini


###########################################################################
# PHP REDIS EXTENSION
###########################################################################

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis


###########################################################################
# Opcache:
###########################################################################

RUN docker-php-ext-install opcache

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini


#
#--------------------------------------------------------------------------
# Clean Up
#--------------------------------------------------------------------------
#

RUN docker-php-source delete \
    \
    && runDeps="$( \
    	scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
    		| tr ',' '\n' \
    		| sort -u \
    		| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )" \
    && apk add --no-cache --virtual .php-rundeps $runDeps \
    \
    && apk del .build-deps


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

COPY ./app.ini /usr/local/etc/php/conf.d
COPY ./php.ini /usr/local/etc/php/conf.d

WORKDIR /var/www

EXPOSE 9000

CMD ["php-fpm"]