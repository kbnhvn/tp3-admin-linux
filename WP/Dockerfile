FROM ubuntu:focal

# Éviter les questions interactives lors de l'installation de paquets
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update; \
    apt-get -y install  openssh-server passwd sudo nano software-properties-common nginx mariadb-client; \
    apt-get -qq clean; \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN apt-get update && apt-get install -y iproute2 git curl iputils-ping net-tools wget curl

RUN add-apt-repository ppa:ondrej/php;\
    apt-get update && apt-get install -y php8.0 php8.0-fpm php8.0-curl php8.0-mysql php8.0-gd php8.0-mbstring php8.0-xml php8.0-zip

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*; \
    rm -f /etc/systemd/system/*.wants/*; \
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*; \
    rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN systemctl enable ssh.service;

RUN systemctl enable nginx;

RUN useradd --create-home -s /bin/bash vagrant; \
    echo -e "vagrant\nvagrant" | (passwd --stdin vagrant); \
    echo 'vagrant ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/vagrant; \
    chmod 440 /etc/sudoers.d/vagrant

RUN mkdir -p /home/vagrant/.ssh; \
    chmod 700 /home/vagrant/.ssh
ADD https://raw.githubusercontent.com/hashicorp/vagrant/master/keys/vagrant.pub /home/vagrant/.ssh/authorized_keys
RUN chmod 600 /home/vagrant/.ssh/authorized_keys; \
    chown -R vagrant:vagrant /home/vagrant/.ssh

# ---- Mise en place de Wordpress ---- #

RUN cd /var/www/html; \
    wget https://wordpress.org/latest.tar.gz; \
    tar -zxvf latest.tar.gz

RUN mv /etc/php/8.0/fpm/php.ini /etc/php/8.0/fpm/php.ini.bak

COPY php.ini /etc/php/8.0/fpm/php.ini

COPY wp_config.php /var/www/html/wordpress/wp-config.php

RUN chown -R www-data:www-data /var/www/html/wordpress; \
    chmod -R 755 /var/www/html/wordpress

COPY wp.conf /etc/nginx/conf.d
# ------------------------------------ #

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]

