FROM php:7.2-apache

COPY ./.docker/apache/config/000-default.conf /etc/apache2/sites-available/000-default.conf

RUN usermod -u 1000 www-data && \
    groupmod -g 1000 www-data