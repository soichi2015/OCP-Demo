# Ansible Tower Dockerfie
FROM registry.access.redhat.com/rhel

WORKDIR /opt

ENV ANSIBLE_TOWER_VER 3.2.5
ENV PG_DATA /var/lib/postgresql/9.6/main
ENV AWX_PROJECTS /var/lib/awx/projects

RUN yum -y update

# Set locale
RUN yum clean all
RUN rm -f /etc/rpm/macros.image-language-conf && \
      sed -i '/^override_install_langs=/d' /etc/yum.conf && \
      yum -y reinstall glibc-common
env LANG=ja_JP.UTF-8 \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8"
RUN yum -y reinstall tzdata && \
    yum -y install yum-langpacks system-config-language &&
    ln -snf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    sed -ri 's/en_US/ja_JP/' /etc/locale.conf

# Use python >= 2.7.9
#RUN apt-get install -y software-properties-common \
#	&& apt-add-repository -y ppa:fkrull/deadsnakes-python2.7 \
#	&& apt-key adv --keyserver keyserver.ubuntu.com --recv 5BB92C09DB82666C \
#	&& apt-get update

# Install libpython2.7; missing dependency in Tower setup
RUN yum install -y python27u python27u-libs python27u-devel

# create /var/log/tower
RUN mkdir -p /var/log/tower

# Download & extract Tower tarball
ADD http://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-${ANSIBLE_TOWER_VER}.tar.gz ansible-tower-setup-${ANSIBLE_TOWER_VER}.tar.gz
RUN tar xvf ansible-tower-setup-${ANSIBLE_TOWER_VER}.tar.gz \
    && rm -f ansible-tower-setup-${ANSIBLE_TOWER_VER}.tar.gz

WORKDIR /opt/ansible-tower-setup-${ANSIBLE_TOWER_VER}
ADD inventory inventory

# Tower setup
RUN ./setup.sh

# Docker entrypoint script
ADD docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

# volumes and ports
VOLUME ["${PG_DATA}", "${AWX_PROJECTS}", "/certs",]
EXPOSE 443

CMD ["/docker-entrypoint.sh", "ansible-tower"]
