FROM php:7.4-fpm-buster

RUN echo "Acquire::http::Pipeline-Depth 0;" > /etc/apt/apt.conf.d/privet && echo "Acquire::http::No-Cache true;" >> /etc/apt/apt.conf.d/privet && echo "Acquire::BrokenProxy    true;" >> /etc/apt/apt.conf.d/privet

RUN apt-get update

RUN apt-get -qq install -y \
    apt-utils \
    git \
    curl \
    unzip \
    vim \
    nano \
    libzip-dev \
    libmcrypt-dev \
    libicu-dev \
    libxml2-dev libxslt1-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    mariadb-client \
    mailutils \
    golang-go \
    && docker-php-ext-configure hash --with-mhash \
    && docker-php-ext-configure gd --with-jpeg --with-freetype \
    && docker-php-ext-install -j$(nproc) intl xsl gd zip pdo_mysql opcache soap bcmath json iconv pcntl sockets

# Enable Xdebug
RUN pecl install xdebug-3.0.3 && docker-php-ext-enable xdebug

# Configure MailHog
RUN go get github.com/mailhog/mhsendmail && cp /root/go/bin/mhsendmail /usr/local/bin/
RUN echo 'sendmail_path="/usr/local/bin/mhsendmail --smtp-addr=mailhog:1025"' >> /usr/local/etc/php/conf.d/mail.ini

# Set memory limit
RUN echo "memory_limit=4G" >> /usr/local/etc/php/conf.d/memory_limit.ini

# Install Composer
RUN cd /usr/src && curl https://getcomposer.org/download/1.10.19/composer.phar --output composer.phar | php
RUN cd /usr/src && mv composer.phar /usr/bin/composer
RUN chmod +x /usr/bin/composer

# Install n98-magerun2
RUN cd /usr/local/bin && curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar
RUN mv /usr/local/bin/n98-magerun2-latest.phar /usr/local/bin/n98-magerun2.phar
RUN chmod +x /usr/local/bin/n98-magerun2.phar

# Add https://github.com/hirak/prestissimo support
RUN composer global require hirak/prestissimo

WORKDIR /var/www/magento

CMD php-fpm
