ARG ZABBIX_BASE
FROM ${ZABBIX_BASE:-maborak/zabbix-base} AS base

# Use the trafex/php-nginx image with PHP 8.3
FROM trafex/php-nginx:3.6.0
WORKDIR /var/www/html
COPY --from=base /var/lib/zabbix_ui/ ./
USER root
RUN apk --no-cache add \
  php83-bcmath \
  php83-xml \
  php83-sockets \
  php83-xmlwriter \
  php83-gettext \
  php83-ldap \
  php83-zip
RUN chown -Rf nobody:nobody /var/www/html/conf/
USER nobody

