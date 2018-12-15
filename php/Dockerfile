FROM ubuntu

ENV DEBIAN_FRONTEND noninteractive

ARG X_DEBUG_REMOTE_HOST
ARG X_DEBUG_PROFILER_ENABLE
ARG USE_BLACKFIRE
ARG USE_HOST_ASSETS_BUILDER

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

RUN if [ ${USE_HOST_ASSETS_BUILDER} = false ]; then \
    curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh && \
    sh nodesource_setup.sh && \
    apt-get -y install nodejs && \
    npm install yarn -g && \
    apt-get -y install default-jre \
;fi


RUN if [ ${USE_BLACKFIRE} = true ]; then \
    version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl --insecure -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r 'echo ini_get("extension_dir");')/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /etc/php/7.0/mods-available/blackfire.ini \
    && ln /etc/php/7.0/mods-available/blackfire.ini /etc/php/7.0/fpm/conf.d/20-blackfire.ini \
;fi
#RUN chown www:www /app

ENV PHP_IDE_CONFIG "serverName=vi.local"

CMD ["php-fpm7.0", "-F"]
