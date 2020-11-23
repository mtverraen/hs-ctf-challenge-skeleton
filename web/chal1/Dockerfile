FROM php:7-apache

COPY src/ /var/www/html

RUN sed -i 's/-Indexes/+Indexes/' /etc/apache2/conf-available/docker-php.conf

ENTRYPOINT apache2-foreground
