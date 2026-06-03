# =========================
# Base Image
# =========================
ARG ALPINE_VERSION=3.21
FROM alpine:${ALPINE_VERSION}

LABEL maintainer="Tim de Pater <code@trafex.nl>" \
      description="Lightweight container with Nginx 1.26 & PHP 8.4 based on Alpine Linux."

WORKDIR /var/www/html

# =========================
# Install Dependencies
# =========================
RUN apk add --no-cache \
    curl \
    nginx \
    supervisor \
    php84 \
    php84-curl \
    php84-dom \
    php84-fileinfo \
    php84-fpm \
    php84-gd \
    php84-intl \
    php84-mbstring \
    php84-mysqli \
    php84-pdo \
    php84-pdo_mysql \
    php84-opcache \
    php84-phar \
    php84-session \
    php84-tokenizer \
    php84-xml \
    php84-xmlreader \
    php84-xmlwriter \
  && ln -s /usr/bin/php84 /usr/bin/php \
  && rm -rf /var/cache/apk/*

# Notes:
# - php84-ctype, php84-openssl are core in PHP 8.4 → no need to install separately
# - cleaned apk cache to reduce image size

# =========================
# Configuration
# =========================
ENV PHP_INI_DIR=/etc/php84

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
