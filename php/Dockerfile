FROM ubuntu

ENV DEBIAN_FRONTEND noninteractive

ARG X_DEBUG_REMOTE_HOST
ARG X_DEBUG_PROFILER_ENABLE

RUN addgroup -gid 1000 www
RUN adduser www -uid 1000 --disabled-login --ingroup www --no-create-home --quiet --system

RUN apt-get update --fix-missing
RUN apt-get install -y software-properties-common
RUN add-apt-repository -y ppa:ondrej/php | tail -2

RUN apt-get install -y \
     php7.0-fpm \
     php7.0-mysql \
     php7.0-ldap \
     php7.0-mbstring \
     php-memcache \
     php7.0-curl  \
     php-imagick \
     php7.0-gd \
     php7.0-soap \
     php-xdebug \
     php7.0-xml \
     php7.0-zip \
     curl

COPY conf/php-fpm.conf /etc/php/7.0/fpm/php-fpm.conf
COPY conf/vi-fpm.conf /etc/php/7.0/fpm/pool.d/vi.conf
COPY ini/xdebug.ini /etc/php/7.0/mods-available/xdebug.ini

RUN rm -f /etc/php/7.0/fpm/pool.d/www.conf

RUN echo "xdebug.remote_host = ${X_DEBUG_REMOTE_HOST};" >> /etc/php/7.0/mods-available/xdebug.ini

RUN if [ ${X_DEBUG_PROFILER_ENABLE} = false ]; then \
    echo "xdebug.profiler_enable = off;" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.profiler_enable_trigger = off;" >> /etc/php/7.0/mods-available/xdebug.ini && \
    echo "xdebug.profiler_append = off;" >> /etc/php/7.0/mods-available/xdebug.ini \
;fi

ENV PHP_IDE_CONFIG "serverName=vi.local"

CMD ["php-fpm7.0", "-F"]
