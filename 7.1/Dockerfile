FROM ubuntu:xenial
MAINTAINER romeOz <serggalka@gmail.com>

ENV OS_LOCALE="en_US.UTF-8"
RUN apt-get update && apt-get install -y locales && locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
    LANGUAGE=${OS_LOCALE} \
    LC_ALL=${OS_LOCALE} \
    DEBIAN_FRONTEND=noninteractive

ENV TZ=Asia/Ho_Chi_Minh
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ENV APACHE_CONF_DIR=/etc/apache2 \
    PHP_CONF_DIR=/etc/php/7.1 \
    PHP_DATA_DIR=/var/lib/php

COPY ./app /var/www/app/
COPY entrypoint.sh /sbin/entrypoint.sh

RUN dpkg-reconfigure locales
RUN apt-get install --no-install-recommends -y software-properties-common
RUN apt-get install --no-install-recommends -y python-software-properties
RUN add-apt-repository -y ppa:ondrej/php
RUN add-apt-repository -y ppa:ondrej/apache2
RUN apt-get update
RUN apt-get install -y curl apache2
RUN apt-get install -y libapache2-mod-php7.1
RUN apt-get install -y php7.1-cli
RUN apt-get install -y php7.1-readline
RUN apt-get install -y php7.1-mbstring
RUN apt-get install -y php7.1-zip
RUN apt-get install -y php7.1-intl
RUN apt-get install -y php7.1-xml
RUN apt-get install -y php7.1-json
RUN apt-get install -y php7.1-curl
RUN apt-get install -y php7.1-mcrypt
RUN apt-get install -y php7.1-gd
RUN apt-get install -y php7.1-pgsql
RUN apt-get install -y php7.1-mysql
RUN apt-get install -y php7.1-bcmath
RUN apt-get install -y php7.1-common
RUN apt-get install -y php7.1-imap
RUN apt-get install -y php-pear

# Apache settings
RUN cp /dev/null ${APACHE_CONF_DIR}/conf-available/other-vhosts-access-log.conf
RUN rm ${APACHE_CONF_DIR}/sites-enabled/000-default.conf ${APACHE_CONF_DIR}/sites-available/000-default.conf
RUN a2enmod rewrite php7.1

# PHP settings
RUN phpenmod mcrypt

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --version=1.6.4 --install-dir=/usr/local/bin --filename=composer

# Cleaning
RUN apt-get purge -y --auto-remove $BUILD_DEPS
RUN apt-get autoremove -y
RUN rm -rf /var/lib/apt/lists/*
# Forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/apache2/access.log
RUN ln -sf /dev/stderr /var/log/apache2/error.log
RUN chmod 755 /sbin/entrypoint.sh
RUN chown www-data:www-data ${PHP_DATA_DIR} -Rf

COPY ./configs/apache2.conf ${APACHE_CONF_DIR}/apache2.conf
COPY ./configs/app.conf ${APACHE_CONF_DIR}/sites-enabled/app.conf
COPY ./configs/php.ini  ${PHP_CONF_DIR}/apache2/conf.d/custom.ini

WORKDIR /var/www/html/

EXPOSE 80 443

# By default, simply start apache.
CMD ["/sbin/entrypoint.sh"]