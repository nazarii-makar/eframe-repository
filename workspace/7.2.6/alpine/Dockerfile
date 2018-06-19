FROM php:7.2.6-alpine

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#
RUN apk add --no-cache --virtual .build-deps \
        build-base \
        libmcrypt-dev \
        libxml2-dev \
        zlib-dev \
        autoconf \
        cyrus-sasl-dev \
        libgsasl-dev


RUN docker-php-ext-install mysqli mbstring pdo pdo_mysql tokenizer xml pcntl


RUN pecl channel-update pecl.php.net


###########################################################################
# PHP REDIS EXTENSION
###########################################################################

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis


###########################################################################
# Crontab
###########################################################################

RUN mkdir /etc/cron.d && \
    chmod -R 644 /etc/cron.d


#####################################
# Composer:
#####################################

# Install composer and add its bin to the PATH.
RUN curl -s http://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer


#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# If you need to modify this image, feel free to do it right here.
#
    # -- Your awesome modifications go here -- #


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

WORKDIR /var/www

CMD ["crond", "-f"]