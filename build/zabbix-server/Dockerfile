ARG ZABBIX_BASE
FROM ${ZABBIX_BASE:-maborak/zabbix-base} AS base

FROM ubuntu:noble
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
    libmysqlclient21 \
    libopenipmi0 \
    libpcre3 \
    libsnmp-dev \
    libssh2-1-dev \
    libevent-pthreads-2.1-7 \
    libssl-dev \
    libxml2 \
    mysql-client \
    snmp-mibs-downloader \
    unixodbc
RUN groupadd --system --gid 1995 zabbix && \
    useradd -r -u 1997 --home /var/lib/zabbix --gid zabbix -s /sbin/nologin zabbix

    USER 1997
EXPOSE 9997
ENTRYPOINT ["/var/lib/zabbix/sbin/zabbix_server", "-f", "-c", "/var/lib/zabbix/etc/zabbix_server.conf"]
