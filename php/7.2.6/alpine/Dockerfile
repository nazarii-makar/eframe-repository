FROM php:7.2.6-alpine

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#
RUN apk add --no-cache --virtual .persistent-deps \
        supervisor

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


#
#--------------------------------------------------------------------------
# Optional Supervisord Configuration
#--------------------------------------------------------------------------
#
# Modify the ./supervisor.conf file to match your App's requirements.
# Make sure you rebuild your container with every change.
#

COPY supervisord.conf /etc/supervisord.conf
COPY supervisord.d /etc/supervisord.d

ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c",  "/etc/supervisord.conf"]


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

WORKDIR /etc/supervisor/conf.d/