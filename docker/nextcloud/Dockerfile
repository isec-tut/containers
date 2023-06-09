# syntax = docker/dockerfile:1.4
FROM nextcloud:27.0.0-fpm

SHELL ["/bin/bash", "-e", "-c"]
ENV DEBIAN_FRONTEND noninteractive
ENV NEXTCLOUD_UPDATE=1

RUN <<EOT
apt-get update
apt-get install -y \
  imagemagick \
  iproute2 \
  libbz2-dev \
  libmagickwand-dev \
  libsmbclient-dev \
  smbclient
apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false
rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

pecl install smbclient
docker-php-ext-enable smbclient
docker-php-ext-install bz2
rm -r /tmp/pear

cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

sed -ie 's!exec "$@"!!g' /entrypoint.sh
cat << 'EOF' >> /entrypoint.sh
for script in $(ls /custom-entrypoint/*.sh | sort); do
  /bin/sh "$script"
done

exec "$@"
EOF
EOT

COPY --chmod=755 scripts/*.sh /
COPY config/zz-docker.conf /usr/local/etc/php-fpm.d/
COPY config/nextcloud.ini /usr/local/etc/php/conf.d/

ENTRYPOINT ["/init.sh", "/entrypoint.sh"]
CMD ["php-fpm"]