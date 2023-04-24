ARG ZABBIX_BASE
FROM maborak/${ZABBIX_BASE:-zabbix-base} AS base

FROM trafex/php-nginx:latest
WORKDIR /var/www/html
COPY --from=base /var/lib/zabbix_ui/ ./
USER root
RUN apk --no-cache add \
  php81-bcmath \
  php81-xml \
  php81-sockets \
  php81-xmlwriter \
  php81-gettext \
  php81-ldap \
  php81-zip
RUN chown -Rf nobody:nobody /var/www/html/conf/
USER nobody

