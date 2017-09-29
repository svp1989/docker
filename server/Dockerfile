#Версия ubuntu которую будем ставить
FROM ubuntu:latest

RUN apt-get update

RUN apt-get install -y software-properties-common \
        python-software-properties \
        language-pack-en-base

#Покаль
RUN LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php

#Ставим ssh создаем папки прописываем пермишины
RUN apt-get update \
        && apt-get upgrade -y \
        && apt-get install -y openssh-server \
        && mkdir /var/run/sshd \
        && mkdir -p /etc/nginx/ssl \
        && mkdir -p /etc/nginx/stream.d \
        && mkdir -p /var/log/php-fpm \
        && mkdir -p /run/php \
        && echo 'root:root' | chpasswd \
        && sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
        && sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

#Устанавливаем основное рабочее окружение
RUN apt-get install -y nginx \
        nano \
        curl \
        npm \
        nodejs \
        composer \
        redis-server \
        supervisor \
        php7.1-fpm \
        php7.1-xml \
        php7.1-mbstring \
        php7.1-mysql \
        php7.1-dev \
        php7.1-xdebug \
        php-pear \
        php-redis

#Ствавим xdebug
RUN yes | pecl install xdebug


#Удаляем лишнее
RUN apt-get remove --purge -y software-properties-common python-software-properties

RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean


#Копируем конфиги и сертификаты
ADD configs/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD nginx.conf/default /etc/nginx/sites-available/
ADD nginx.conf/websocket.conf /etc/nginx/stream.d/
ADD ssl/* /etc/nginx/ssl/
ADD configs/xdebug.ini /etc/php/7.1/mods-available/

ENV NOTVISIBLE "in users profile"

#Редактируем профиль
RUN echo "export VISIBLE=now" >> /etc/profile \
        && echo "export VISIBLE=now" >> /etc/profile \
        && echo "include /etc/nginx/stream.d/*.conf;" >> /etc/nginx/nginx.conf \
        && echo "daemon off;" >> /etc/nginx/nginx.conf \
        && sed -e 's/;daemonize = yes/daemonize = no/' /etc/php/7.1/fpm/php-fpm.conf

#Инициализируем запуск прилодения типа nano
RUN export TERM=xterm

CMD ["/usr/bin/supervisord"]
