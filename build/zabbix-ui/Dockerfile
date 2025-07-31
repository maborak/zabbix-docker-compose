ARG ZABBIX_BASE
FROM ${ZABBIX_BASE:-maborak/zabbix-base} AS base

# Use the trafex/php-nginx image with PHP 8.4
FROM trafex/php-nginx:3.9.0
WORKDIR /var/www/html
COPY --from=base /var/lib/zabbix_ui/ ./
USER root
RUN apk --no-cache add \
  php84-bcmath \
  php84-sockets \
  php84-xmlwriter \
  php84-gettext \
  php84-ldap \
  php84-zip
RUN chown -Rf nobody:nobody /var/www/html/conf/
USER nobody

