FROM debian:wheezy
MAINTAINER Philippe Pepiot <phil@philpep.org>

RUN printf 'APT::Install-Recommends "0";\nAPT::Install-Suggests "0";\n' > /etc/apt/apt.conf.d/no-recommands
RUN echo "deb-src http://httpredir.debian.org/debian wheezy main" >> /etc/apt/sources.list && \
    echo "deb-src http://httpredir.debian.org/debian wheezy-updates main" >> /etc/apt/sources.list && \
    echo "deb-src http://security.debian.org wheezy/updates main" >> /etc/apt/sources.list


RUN apt-get -qqy update && apt-get -y install \
    apt-transport-https ca-certificates curl \
    openjdk-7-jre-headless sudo ssh locales \
    build-essential fontconfig git libevent-dev \
    libfreetype6-dev libjpeg-dev libpng-dev libsmbclient-dev libssl-dev \
    libxml2-dev libxslt1-dev lsb-release pdftk pkg-config redis-server rsync \
    ttf-dejavu wget zlib1g-dev \
    python python-dev \
    python-pip

# Locales
RUN echo "LANG=en_US.UTF-8" > /etc/default/locale && \
    echo "LANGUAGE=en_US:en" >> /etc/default/locale && \
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen && \
    update-locale

# Python
RUN pip install -U 'pip>=7,<8'
RUN pip install -U 'virtualenv>=13,<14' 'tox>=2,<3' && \
    rm -rf /root/.cache/pip

# PostgreSQL
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ wheezy-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    wget -q -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - && \
    apt-get -qqy update && apt-get -y install \
    postgresql-9.4 postgresql-server-dev-9.4 postgresql-contrib-9.4 && \
    service postgresql start && \
    su - postgres -c 'createuser -s jenkins; createdb -O jenkins jenkins'

# Jenkins user
RUN useradd -m -s /bin/bash jenkins && \
    echo jenkins:jenkins | chpasswd && \
    echo "jenkins ALL=(root) NOPASSWD: ALL" > /etc/sudoers.d/jenkins && \
    chmod 440 /etc/sudoers.d/jenkins && \
    mkdir -p /var/run/sshd

EXPOSE 22
VOLUME /var/lib/docker
CMD ["/usr/sbin/sshd", "-D"]
