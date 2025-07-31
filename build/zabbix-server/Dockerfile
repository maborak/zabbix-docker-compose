ARG ZABBIX_BASE
ARG UBUNTU_VERSION=25.04
FROM ${ZABBIX_BASE:-maborak/zabbix-base} AS base

FROM ubuntu:${UBUNTU_VERSION}
WORKDIR /var/lib/zabbix/
COPY --from=base /var/lib/zabbix/ ./
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get install -y \
    bash \
    tzdata \
    ca-certificates \
    iputils-ping \
    traceroute \
    fping \
    libcurl4 \
    libevent-2.1 \
    libmysqlclient24 \
    libopenipmi0 \
    libpcre3 \
    libsnmp-dev \
    libssh2-1-dev \
    libevent-dev \
    libevent-extra* \
    libevent-pthreads* \
    libssl-dev \
    libxml2 \
    mysql-client \
    net-tools \
    snmp-mibs-downloader \
    unixodbc \
    supervisor
RUN groupadd --system --gid 1995 zabbix && \
    useradd -r -u 1997 --home /var/lib/zabbix --gid zabbix -s /sbin/nologin zabbix

# Copy the configuration files
COPY zabbix_server.conf /var/lib/zabbix/etc/zabbix_server.conf
COPY zabbix_agent2.conf /var/lib/zabbix/etc/zabbix_agent2.conf

# Create supervisor configuration and log directories
RUN mkdir -p /etc/supervisor/conf.d /var/lib/zabbix/log /var/lib/zabbix/run /var/lib/zabbix/etc/zabbix_agent2.d
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
RUN chown -R zabbix:zabbix /var/lib/zabbix/log /var/lib/zabbix/run

USER 1997
EXPOSE 9997 10050
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
