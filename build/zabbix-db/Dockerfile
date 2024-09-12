ARG ZABBIX_BASE
FROM ${ZABBIX_BASE:-maborak/zabbix-base} AS base

FROM mariadb:10-ubi
WORKDIR /docker-entrypoint-initdb.d/
COPY --from=base /var/lib/zabbix_db/mysql/schema.sql ./01-schema.sql
COPY --from=base /var/lib/zabbix_db/mysql/images.sql ./02-images.sql
COPY --from=base /var/lib/zabbix_db/mysql/data.sql ./03-data.sql