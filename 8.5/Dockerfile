# =========================
# Base Image
# =========================
ARG ALPINE_VERSION=3.24
FROM alpine:${ALPINE_VERSION}

LABEL maintainer="Tim de Pater <code@trafex.nl>" \
      description="Lightweight container with Nginx & PHP 8.5 based on Alpine Linux."

WORKDIR /var/www/html

# =========================
# Install Dependencies
# =========================
RUN apk update && apk upgrade --no-cache \
  && apk add --no-cache \
    --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    --repository=https://dl-cdn.alpinelinux.org/alpine/edge/main \
    --repository=https://dl-cdn.alpinelinux.org/alpine/v3.24/community \
    --repository=https://dl-cdn.alpinelinux.org/alpine/v3.24/main \
    curl \
    nginx \
    supervisor \
    php85 \
    php85-curl \
    php85-dom \
    php85-fileinfo \
    php85-fpm \
    php85-gd \
    php85-intl \
    php85-mbstring \
    php85-mysqli \
    php85-pdo \
    php85-pdo_mysql \
    php85-opcache \
    php85-phar \
    php85-pecl-redis \
    php85-session \
    php85-tokenizer \
    php85-xml \
    php85-xmlreader \
    php85-xmlwriter \
  && ln -s /usr/bin/php85 /usr/bin/php \
  && rm -rf /var/cache/apk/*

# =========================
# Configuration
# =========================
ENV PHP_INI_DIR=/etc/php85

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
# Application
# =========================
COPY --chown=nobody src/ /var/www/html/

# =========================
# Runtime
# =========================
EXPOSE 8080
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping || exit 1

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
