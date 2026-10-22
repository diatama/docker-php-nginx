# =========================
# Base Image
# =========================
ARG ALPINE_VERSION=3.24
FROM alpine:${ALPINE_VERSION}

LABEL maintainer="Tim de Pater <code@trafex.nl>" \
      description="Lightweight container with Nginx & PHP 8.2 based on Alpine Linux."

# Setup document root
WORKDIR /var/www/html

# Install dependencies
RUN apk update && apk upgrade --no-cache \
    && apk add --no-cache \
       --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
       --repository=https://dl-cdn.alpinelinux.org/alpine/edge/main \
       curl \
       nginx \
       supervisor \
       php82 \
       php82-bcmath \
       php82-ctype \
       php82-curl \
       php82-dom \
       php82-fpm \
       php82-gd \
       php82-intl \
       php82-mbstring \
       php82-mysqli \
       php82-opcache \
       php82-openssl \
       php82-pdo_mysql \
       php82-phar \
       php82-pecl-redis \
       php82-session \
       php82-simplexml \
       php82-tokenizer \
       php82-xml \
       php82-xmlreader \
       php82-zip \
    && ln -s /usr/bin/php82 /usr/bin/php \
    && rm -rf /var/cache/apk/*

# =========================
# Configuration
# =========================
ENV PHP_INI_DIR=/etc/php82

# Nginx
COPY config/nginx.conf /etc/nginx/nginx.conf

# PHP-FPM
COPY config/fpm-pool.conf ${PHP_INI_DIR}/php-fpm.d/www.conf
COPY config/php.ini       ${PHP_INI_DIR}/conf.d/custom.ini

# Supervisor
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# =========================
# Permissions & User
# =========================
RUN mkdir -p /run/nginx \
    && chown -R nobody:nobody \
       /var/www/html \
       /run \
       /var/lib/nginx \
       /var/log/nginx

USER nobody

# =========================
# Application Code
# =========================
COPY --chown=nobody src/ /var/www/html/

# =========================
# Runtime
# =========================
EXPOSE 8080
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
