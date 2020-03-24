FROM hyperf/hyperf:7.2-alpine-v3.9-base

LABEL maintainer="Hyperf Developers <group@hyperf.io>" version="1.0" license="MIT"

ARG SWOOLE_VERSION
ARG timezone


##
# ---------- env settings ----------
##
ENV SWOOLE_VERSION=${SWOOLE_VERSION:-"4.4.12"} \
    #  install and remove building packages
    PHPIZE_DEPS="autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev libtool automake" \
    TIMEZONE=${timezone:-"Asia/Shanghai"} \
        COMPOSER_VERSION=1.9.0 \
        APP_ENV=prod

COPY ./swoole-swoole-v4.4.12.zip /tmp/swoole.zip

# update
RUN set -ex \
    && apk update \
    # for swoole extension libaio linux-headers
    && apk add --no-cache libstdc++ openssl git bash imagemagick \
    && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libaio-dev openssl-dev imagemagick-dev \
    # download
#    && cd /tmp \
#    && curl -SL "https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz" -o swoole.tar.gz \
#    && ls -alh \
    # php extension:swoole
    && cd /tmp \
#    && mkdir -p swoole \
#    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && ls \
    && unzip swoole.zip > /dev/null \
    && ( \
        cd swoole \
        && phpize \
        && ./configure --enable-mysqlnd --enable-openssl --enable-http2 \
        && make -s -j$(nproc) && make install \
    ) \
    && echo "extension=swoole.so" > /etc/php7/conf.d/50_swoole.ini \
    && echo "swoole.use_shortname = 'Off'" >> /etc/php7/conf.d/50_swoole.ini \

    # 安装imagick
    && curl -SL "https://pecl.php.net/get/imagick-3.4.4.tgz" -o imagick.tgz \
    && mkdir imagick \
    && tar -xf imagick.tgz -C imagick --strip-components=1  \
    && ( \
        cd imagick \
            && phpize \
            && ./configure \
            && make && make install \
    ) \
    && echo "extension=imagick.so" > /etc/php7/conf.d/imagick.ini \
    # clear
    && php -v \
    && php -m \
    && php --ri swoole \
    && php --ri imagick \
    # ---------- clear works ----------
    && apk del .build-deps \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"

# update
RUN set -ex \
    && apk update \
    && cd /tmp \
    && wget https://github.com/composer/composer/releases/download/${COMPOSER_VERSION}/composer.phar \
    && chmod u+x composer.phar \
    && mv composer.phar /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # show php version and extensions
    #  ---------- some config ----------
    && cd /etc/php7 \
    # - config PHP
    && { \
        echo "upload_max_filesize=100M"; \
        echo "post_max_size=108M"; \
        echo "memory_limit=1024M"; \
        echo "date.timezone=${TIMEZONE}"; \
    } | tee conf.d/99-overrides.ini \
    # - config timezone
    && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && echo "${TIMEZONE}" > /etc/timezone \
    # ---------- clear works ----------
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"

WORKDIR /opt/www

# Composer Cache
# COPY ./composer.* /opt/www/
# RUN composer install --no-dev

#COPY . /opt/www
#RUN composer install --no-dev \
#    && composer dump-autoload -o \
#    && composer init-proxy

EXPOSE 9501

#ENTRYPOINT ["php", "/opt/www/bin/hyperf.php", "start"]

#ENTRYPOINT ["./start"]
