FROM phpdockerio/php:8.1-fpm
WORKDIR "/application"

ARG NODE_VERSION=16

RUN apt-get update \
    && apt-get -y --no-install-recommends install git php8.1-mysql \
    && apt-get clean \
    && curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm \
    && apt-get update \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
