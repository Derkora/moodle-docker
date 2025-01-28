# Use the official PHP 8.3 image as a base
FROM php:8.3-apache

# Set environment variables # Moodle 4.5
ENV MOODLE_VERSION 405
ENV MOODLE_GIT_REPO https://github.com/moodle/moodle.git

# Install necessary system dependencies
RUN apt-get update && apt-get install -y \
    git \
    libzip-dev \
    libpng-dev \
    libicu-dev \
    libxml2-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libonig-dev \
    libcurl4-openssl-dev \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install zip \
    && docker-php-ext-install intl \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install soap \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install curl

# Clone Moodle from GitHub
RUN git clone --branch MOODLE_${MOODLE_VERSION}_STABLE ${MOODLE_GIT_REPO} /var/www/html

# Set PHP settings for Moodle
RUN echo "max_input_vars=5000" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.memory_consumption=128" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.max_accelerated_files=10000" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.revalidate_freq=60" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.enable_cli=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.use_cwd=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.validate_timestamps=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.save_comments=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.enable_file_override=0" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.interned_strings_buffer=8" >> /usr/local/etc/php/conf.d/moodle.ini 

# Create moodledata directory
RUN mkdir -p /var/www/moodledata && \
    chown -R www-data:www-data /var/www/moodledata && \
    chmod -R 777 /var/www/moodledata

# Set working directory
WORKDIR /var/www/html

# Set the correct permissions for Moodle files
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 755 /var/www/html

# Expose port 80
EXPOSE 80