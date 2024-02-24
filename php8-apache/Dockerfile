
FROM php:8.1-apache
ARG TIMEZONE

RUN a2enmod rewrite

RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer --version

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
    && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
    && "date"

RUN apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql

#RUN apt-get install php8.1-xsl
RUN apt-get install -y zlib1g-dev
RUN docker-php-ext-install zlib; exit 0
#Now we rename the in step 1 downloaded file to desired filename
RUN cp /usr/src/php/ext/zlib/config0.m4 /usr/src/php/ext/zlib/config.m4
#And try to install extension again, this time it works
RUN docker-php-ext-install zlib
# Type docker-php-ext-install to see available extensions
RUN docker-php-ext-install pdo pdo_mysql exif 

RUN apt-get update \
    && apt-get install -y libzip-dev \
    && docker-php-ext-install zip

RUN apt install python3-pip -y
RUN apt-get install -y python3-bs4

RUN echo 'alias sf="php app/console"' >> ~/.bashrc \
    && echo 'alias sf3="php bin/console"' >> ~/.bashrc

COPY ./apache.conf /etc/apache2/sites-available/000-default.conf
RUN a2ensite 000-default.conf

WORKDIR /var/www/symfony

CMD ["apache2-foreground"]
