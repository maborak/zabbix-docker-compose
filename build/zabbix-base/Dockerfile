FROM ubuntu:21.10
RUN apt-get update
ARG DEBIAN_FRONTEND=noninteractive
WORKDIR /build
RUN apt-get -y install mariadb-client \
    mariadb-common \
    software-properties-common \
    ca-certificates \
    libpcre2-8-0 \
    wget \
    build-essential \
    automake \
    pkg-config \
    autoconf \
    autogen \
    libmysqlclient-dev \
    libxml2-dev \
    libsnmp-dev \
    libssh2-1-dev \
    libopenipmi-dev \
    libevent-dev \
    libcurl4-openssl-dev \
    libpcre3-dev \
    unixodbc-dev \
    openjdk-18-jdk \
    libldap2-dev \
    libgnutls28-dev \
    libmodbus-dev \
    golang-go \
    libmysqlclient-dev

RUN wget https://cdn.zabbix.com/zabbix/sources/development/6.0/zabbix-6.0.0alpha7.tar.gz && \
    tar xvfz zabbix-6.0.0alpha7.tar.gz && \
    cd zabbix-6.0.0alpha7 && \
    ./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2 --with-openipmi --with-ssh2 --with-unixodbc --enable-proxy --enable-java --enable-webservice --enable-ipv6 --with-ldap --enable-agent2 --with-openssl --with-libmodbus --prefix=/var/lib/zabbix && \
    make && \
    make install
