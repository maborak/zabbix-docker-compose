FROM ubuntu:21.10
WORKDIR /build
RUN apt-get update
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get -y install software-properties-common \
    ca-certificates \
    libpcre2-8-0 \
    wget \
    git \
    build-essential \
    automake \
    pkg-config \
    golang \
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
    openjdk-17-jdk \
    libldap2-dev \
    libgnutls28-dev \
    libmodbus-dev \
    curl \
    libmysqlclient-dev && \
    rm -rf /var/lib/apt/lists/*

RUN cd /tmp && \
    git clone https://github.com/zabbix/zabbix.git && \
    cd zabbix/ && \
    sh bootstrap.sh
RUN cd /tmp/zabbix/ && \
    ./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2 --with-openipmi --with-ssh2 --with-unixodbc --enable-proxy --enable-java --enable-webservice --enable-ipv6 --with-ldap --enable-agent2 --with-openssl --with-libmodbus --prefix=/var/lib/zabbix && \
    make && \
    make install && \
    make dbschema && \
    mv /tmp/zabbix/ui /var/lib/zabbix_ui && \
    mv /tmp/zabbix/database /var/lib/zabbix_db && \
    rm -Rf /tmp/zabbix* /tmp/go/

