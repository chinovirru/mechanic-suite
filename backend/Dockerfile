FROM php:8.3-apache

# Set working directory
WORKDIR /app/site

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    libzip-dev \
    default-mysql-client \
    && rm -rf /var/lib/apt/lists/*

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions required for Laravel
RUN docker-php-ext-install pdo pdo_mysql mbstring exif pcntl bcmath gd zip

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Enable Apache modules
RUN a2enmod rewrite
RUN a2enmod headers

# Copy Apache virtual host configuration
COPY ./docker/apache/000-default.conf /etc/apache2/sites-available/000-default.conf

# Copy Laravel application code
COPY ./site /app/site

# Set proper permissions
RUN chown -R www-data:www-data /app/site && \
    chmod -R 755 /app/site && \
    chmod -R 775 /app/site/storage && \
    chmod -R 775 /app/site/bootstrap/cache

# Expose port
EXPOSE 80

# Start Apache
CMD ["apache2-foreground"]
