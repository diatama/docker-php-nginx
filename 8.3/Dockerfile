ARG ALPINE_VERSION=3.24
FROM alpine:${ALPINE_VERSION}
LABEL Maintainer="Tim de Pater <code@trafex.nl>"
LABEL Description="Lightweight container with Nginx 1.26 & PHP 8.3 based on Alpine Linux."
# Setup document root
WORKDIR /var/www/html

# Install packages and remove default server definition
RUN apk update && apk upgrade --no-cache \
  && apk add --no-cache \
  --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
  --repository=https://dl-cdn.alpinelinux.org/alpine/edge/main \
  --repository=https://dl-cdn.alpinelinux.org/alpine/v3.24/community \
  --repository=https://dl-cdn.alpinelinux.org/alpine/v3.24/main \
  curl \
  nginx \
  php83 \
  php83-ctype \
  php83-curl \
  php83-dom \
  php83-fileinfo \
  php83-fpm \
  php83-gd \
  php83-intl \
  php83-mbstring \
  php83-mysqli \
  php83-opcache \
  php83-openssl \
  php83-pdo \
  php83-pdo_mysql \
  php83-phar \
  php83-pecl-redis \
  php83-session \
  php83-tokenizer \
  php83-xml \
  php83-xmlreader \
  php83-xmlwriter \
  supervisor \
  && ln -s /usr/bin/php83 /usr/bin/php \
  && rm -rf /var/cache/apk/*

# Configuration
ENV PHP_INI_DIR=/etc/php83

# Nginx
COPY config/nginx.conf /etc/nginx/nginx.conf

# PHP-FPM
COPY config/fpm-pool.conf ${PHP_INI_DIR}/php-fpm.d/www.conf
COPY config/php.ini ${PHP_INI_DIR}/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN mkdir -p /run/nginx \
    && chown -R nobody:nobody /var/www/html /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
COPY --chown=nobody src/ /var/www/html/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping || exit 1