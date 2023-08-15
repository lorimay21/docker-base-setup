FROM php:8.1-apache

WORKDIR /var/www

RUN apt-get update -y && \
  apt-get install -y  \
  curl \
  zlib1g-dev \
  libpq-dev \
  libpng-dev \
  libonig-dev \
  libzip-dev \
  unzip \
  software-properties-common \
  npm \
  cron \
  vim \
  pngquant \
  jpegoptim \
  optipng && \
  apt-get install -y libmagickwand-dev --no-install-recommends && \
  pecl install imagick && docker-php-ext-enable imagick && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* 

RUN docker-php-ext-install  \ 
  zip \
  pdo_mysql \
  gd \
  bcmath 

# Install composer
RUN curl -sS https://getcomposer.org/installer | php && \
  mv composer.phar /usr/local/bin/composer && \
  chmod +x /usr/local/bin/composer

# Install NPM
RUN npm install npm@6.14 -g && \
  npm install n -g && \
  n 16

ENV APACHE_DOCUMENT_ROOT /var/www/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
COPY ./docker/apache/php.ini /usr/local/etc/php/
RUN a2enmod rewrite

COPY ./src/composer.* /var/www/
RUN composer install  --no-scripts --no-autoloader
COPY ./src /var/www/
RUN set -eux \
  && composer dump-autoload --no-scripts \
  && chmod -R a=rwX ./storage

# Install Crontab
COPY ./docker/apache/crontab /etc/cron.d/crontab
RUN chmod 0644 /etc/cron.d/crontab
RUN crontab /etc/cron.d/crontab
RUN mkdir -p /var/log/cron
RUN sed -i 's/^exec /service cron start\n\nexec /' /usr/local/bin/apache2-foreground
