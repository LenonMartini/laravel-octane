FROM php:8.2-fpm

# Arguments
ARG user=lenon
ARG uid=1000

# Install system dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        curl \
        libpng-dev \
        libonig-dev \
        libxml2-dev \
        zip \
        unzip \
        supervisor \
        libbrotli-dev \
        dos2unix \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd sockets

# Install Swoole
RUN pecl install swoole \
    && docker-php-ext-enable swoole

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user \
    && mkdir -p /home/$user/.composer \
    && chown -R $user:$user /home/$user

# Install redis extension
RUN pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis

# Set working directory
WORKDIR /var/www

# Copy necessary files and set permissions
COPY start-container /usr/local/bin/start-container
RUN dos2unix /usr/local/bin/start-container && chmod +x /usr/local/bin/start-container

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /usr/local/etc/php/conf.d/custom.ini

# Expose port 8000
EXPOSE 8000

# Entry point command
ENTRYPOINT ["start-container"]
