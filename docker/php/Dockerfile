FROM php:7.2-fpm

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        curl \
        g++ \
        gnupg \
        libssl-dev \
        libmcrypt-dev \
        libmagickwand-dev \
        libicu-dev \
        vim \
        wget \
        zip \
        zlib1g-dev

RUN docker-php-ext-install bcmath && \
    docker-php-ext-install gd zip && \
	docker-php-ext-install pdo_mysql && \
    pecl install imagick && \
    docker-php-ext-enable imagick && \
    docker-php-ext-install sockets && \
    docker-php-ext-install intl

ENV WORKDIR "/var/www/"

#####################################
# xDebug:
#####################################

COPY ./xdebug.ini /tmp/xdebug.ini

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    mv /tmp/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini && \
    pecl install xdebug-2.6.0 && \
    docker-php-ext-enable xdebug \
;fi

#####################################
# Opcache:
#####################################

COPY ./opcache.ini /tmp/opcache.ini

ARG INSTALL_OPCACHE=false
RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache && \
    docker-php-ext-enable opcache && \
    mv /tmp/opcache.ini /usr/local/etc/php/conf.d/opcache.ini \
;fi

#####################################
# Composer
#####################################

ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install composer and add its bin to the PATH.
RUN curl -s http://getcomposer.org/installer | php && \
    echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.bashrc && \
    mv composer.phar /usr/local/bin/composer

RUN composer global require hirak/prestissimo

#####################################
# NODEJS & YARN
#####################################

# Install NODEJS
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash
RUN	apt-get update && \
	apt-get install -y nodejs

# Install Yarn
RUN curl -o- -L https://yarnpkg.com/install.sh | bash

RUN	apt-get update && \
	apt-get install -y yarn

#####################################
# Final Touch
#####################################

ADD ./php.ini /usr/local/etc/php/conf.d
ADD ./php.pool.conf /usr/local/etc/php-fpm.d/

RUN usermod -u 1000 www-data

WORKDIR ${WORKDIR}

EXPOSE 9000

CMD ["php-fpm"]

