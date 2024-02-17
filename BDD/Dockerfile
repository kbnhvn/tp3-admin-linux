FROM ubuntu:focal

RUN apt-get update; \
    apt-get -y install  openssh-server passwd sudo nano software-properties-common; \
    apt-get -qq clean; \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN apt-get update && apt-get install -y iproute2 git curl iputils-ping net-tools wget curl

RUN apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'; \
    add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mariadb.mirror.liquidtelecom.com/repo/10.6/ubuntu focal main'; \
    apt-get update && apt-get install -y mariadb-server mariadb-client;

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*; \
    rm -f /etc/systemd/system/*.wants/*; \
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*; \
    rm -f /lib/systemd/system/anaconda.target.wants/*;

RUN systemctl enable ssh.service;

RUN systemctl enable mariadb;

RUN useradd --create-home -s /bin/bash vagrant; \
    echo -e "vagrant\nvagrant" | (passwd --stdin vagrant); \
    echo 'vagrant ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/vagrant; \
    chmod 440 /etc/sudoers.d/vagrant

RUN mkdir -p /home/vagrant/.ssh; \
    chmod 700 /home/vagrant/.ssh
ADD https://raw.githubusercontent.com/hashicorp/vagrant/master/keys/vagrant.pub /home/vagrant/.ssh/authorized_keys
RUN chmod 600 /home/vagrant/.ssh/authorized_keys; \
    chown -R vagrant:vagrant /home/vagrant/.ssh

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]

