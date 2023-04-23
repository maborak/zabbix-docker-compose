FROM maborak/${ZABBIX_BASE:-zabbix6-base} AS base

FROM ubuntu:22.10
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
    libssl-dev \
    libxml2 \
    mysql-client \
    snmp-mibs-downloader \
    unixodbc
RUN addgroup --system --gid 1995  --quiet zabbix && \
    adduser --quiet --uid 1997 --system --ingroup zabbix --home /var/lib/zabbix --no-create-home zabbix
USER 1997
EXPOSE 9997
ENTRYPOINT ["/var/lib/zabbix/sbin/zabbix_server", "-f", "-c", "/var/lib/zabbix/etc/zabbix_server.conf"]
