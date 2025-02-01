# Dockerfile ketika membuat image moodle 1.1
# Use the official PHP 8.3 image as a base
# Stage 1: Build stage
FROM php:8.3-cli-alpine AS builder

# Set environment variables for Moodle 4.5
ENV MOODLE_VERSION=405
ENV MOODLE_GIT_REPO=https://github.com/moodle/moodle.git

# Install dependencies and PHP extensions
RUN apk add --no-cache \
    git unzip libzip-dev libpng-dev icu-dev libxml2-dev freetype-dev \
    libjpeg-turbo-dev oniguruma-dev curl-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd zip intl mysqli pdo_mysql soap mbstring curl exif opcache

# Clone Moodle from GitHub
RUN git clone --branch MOODLE_${MOODLE_VERSION}_STABLE ${MOODLE_GIT_REPO} /var/www/html && \
    rm -rf /var/www/html/.git

# Set PHP settings for Moodle
RUN echo "max_input_vars=5000" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.memory_consumption=128" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.max_accelerated_files=10000" >> /usr/local/etc/php/conf.d/moodle.ini && \
    echo "opcache.revalidate_freq=60" >> /usr/local/etc/php/conf.d/moodle.ini

# Set the correct permissions for Moodle files
RUN chown -R www-data:www-data /var/www/html && \
    chmod -R 755 /var/www/html && \
    rm -rf /tmp/* /var/cache/apk/*

# Stage 2: Final stage
FROM php:8.3-fpm-alpine

# Install Nginx and Supervisor
RUN apk add --no-cache nginx supervisor \
    libzip-dev libpng-dev icu-dev libxml2-dev freetype-dev \
    libjpeg-turbo-dev oniguruma-dev curl-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd zip intl mysqli pdo_mysql soap mbstring curl exif opcache

# Copy PHP configuration from builder stage
COPY --from=builder /usr/local/etc/php/conf.d/moodle.ini /usr/local/etc/php/conf.d/moodle.ini

# Copy Moodle files from builder stage
COPY --from=builder /var/www/html /var/www/html

# Configure Nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Ensure opcache is enabled
RUN echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/moodle.ini

# Configure PHP-FPM
RUN sed -i 's/;listen.owner = www-data/listen.owner = www-data/g' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/;listen.group = www-data/listen.group = www-data/g' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/user = nobody/user = www-data/g' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/group = nobody/group = www-data/g' /usr/local/etc/php-fpm.d/www.conf

# Configure Supervisor
COPY supervisord.conf /etc/supervisord.conf

# Create moodledata directory
RUN mkdir -p /var/www/moodledata && \
    chown -R www-data:www-data /var/www/moodledata && \
    chmod -R 755 /var/www/moodledata

# Set working directory
WORKDIR /var/www/html

# Expose port 80
EXPOSE 80

# Start Supervisor to manage Nginx and PHP-FPM
CMD ["supervisord", "-c", "/etc/supervisord.conf"]