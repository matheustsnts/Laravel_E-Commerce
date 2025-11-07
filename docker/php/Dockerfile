FROM php:8.3-fpm-alpine

# Instala dependências do sistema e extensões PHP
RUN apk add --no-cache \
    libzip-dev \
    zip \
    unzip \
    git \
    mysql-client \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    nodejs \
    npm

RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

# Instala o Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o usuário (opcional, mas boa prática)
RUN addgroup -g 1000 laravel && adduser -u 1000 -G laravel -s /bin/sh -D laravel
USER laravel

WORKDIR /var/www/html
