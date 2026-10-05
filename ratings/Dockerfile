FROM php:8.5-apache-bookworm

RUN apt-get update \
    && apt-get install -y --no-install-recommends curl unzip libzip-dev \
    && docker-php-ext-install pdo_mysql opcache zip \
    && rm -rf /var/lib/apt/lists/*

# Enable AutoProfile for PHP which is currently opt-in beta
RUN echo "instana.enable_auto_profile=1" > "/usr/local/etc/php/conf.d/zzz-instana-extras.ini"

# relax permissions on status
COPY status.conf /etc/apache2/mods-available/status.conf
# Enable Apache mod_rewrite and status
RUN a2enmod rewrite && a2enmod status

WORKDIR /var/www/html

COPY html/ ./

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
RUN composer install --no-dev --no-interaction --prefer-dist --optimize-autoloader

# This is important. Symfony needs write permissions and we
# dont know the context in which the container will run, i.e.
# which user will be forced from the outside so better play
# safe for this simple demo.
RUN rm -rf /var/www/var/* \
    && chown -R www-data:www-data /var/www \
    && chmod -R g=u /var/www
