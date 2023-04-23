FROM maborak/zabbix6-base-git AS base

FROM trafex/php-nginx
WORKDIR /var/www/html
COPY --from=base /var/lib/zabbix_ui/ ./
USER root
RUN apk --no-cache add \
  php8-bcmath \
  php8-xml \
  php8-sockets \
  php8-xmlwriter \
  php8-gettext \
  php8-ldap \
  php8-zip
USER nobody

