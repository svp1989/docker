FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update

RUN apt-get install -y software-properties-common \
        python-software-properties \
        language-pack-en-base

RUN LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php

RUN apt-get update \
        && apt-get upgrade -y \
        && apt-get install -y openssh-server \
        && mkdir /var/run/sshd \
        && mkdir -p /var/log/php-fpm \
        && mkdir -p /run/php \
        && echo 'root:root' | chpasswd \
        && sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
        && sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

RUN apt-get install -y nginx \
        nano \
        curl \
        redis-server \
        supervisor \
        php7.2-fpm \
        php-redis

# Cleanup
RUN apt-get remove --purge -y software-properties-common python-software-properties

RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean

ADD configs/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD nginx.conf/* /etc/nginx/sites-available/
VOLUME ["/var/www"]

ENV NOTVISIBLE "in users profile"

RUN echo "export VISIBLE=now" >> /etc/profile \
        && echo "export VISIBLE=now" >> /etc/profile \
        && echo "export TERM=xterm" >> /etc/profile \
        && echo "daemon off;" >> /etc/nginx/nginx.conf \
        && sed -e 's/;daemonize = yes/daemonize = no/' /etc/php/7.2/fpm/php-fpm.conf

EXPOSE 443
EXPOSE 9000
EXPOSE 80

CMD ["/usr/bin/supervisord"]