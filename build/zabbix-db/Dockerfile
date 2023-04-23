FROM maborak/zabbix6-base-git AS base

FROM mysql
WORKDIR /docker-entrypoint-initdb.d/
COPY --from=base /var/lib/zabbix_db/mysql/schema.sql ./01-schema.sql
COPY --from=base /var/lib/zabbix_db/mysql/images.sql ./02-images.sql
COPY --from=base /var/lib/zabbix_db/mysql/data.sql ./03-data.sql