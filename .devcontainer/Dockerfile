# Use the official PHP image as the base image
FROM php:7.4-apache

# Install dependencies
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    wget \
    libicu-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libxml2-dev \
    libzip-dev \
    mariadb-client \
    pkg-config \
    libonig-dev \
    && apt-get clean

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) intl mbstring xml zip mysqli pdo pdo_mysql gd exif calendar

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set up MediaWiki
WORKDIR /var/www/html
RUN wget https://releases.wikimedia.org/mediawiki/1.37/mediawiki-1.37.1.tar.gz \
    && tar -xvzf mediawiki-*.tar.gz \
    && mv mediawiki-1.37.1 mediawiki \
    && rm mediawiki-*.tar.gz

# Set file permissions
RUN chown -R www-data:www-data /var/www/html/mediawiki \
    && chmod -R 755 /var/www/html/mediawiki

# Copy custom Apache config
COPY mediawiki.conf /etc/apache2/sites-available/000-default.conf

# Copy the entrypoint script
COPY entrypoint.sh /usr/local/bin/

# Make the entrypoint script executable
RUN chmod +x /usr/local/bin/entrypoint.sh

# Set the entrypoint script as the entrypoint for the container
ENTRYPOINT ["entrypoint.sh"]

# Expose port 80
EXPOSE 80
