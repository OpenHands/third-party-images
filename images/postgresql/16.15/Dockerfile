# syntax=docker/dockerfile:1
# PostgreSQL 16.15 built with the Bitnami 16.4.0 image's configure options, installed over its /opt/bitnami/postgresql.
FROM docker.io/library/debian:bookworm@sha256:854dd8ab51d62866dbd8f5431d15ce77b12983a2a4a1e1fe43a2395dc7ef9f61 AS build
ARG PG_VERSION=16.15
ARG PG_SHA256=c1575341fa7bd40f5274ea465b34390f4dc64cdd0770af327005caaeb9f6b7ed
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       bison build-essential bzip2 ca-certificates curl flex libedit-dev libicu-dev libldap2-dev \
       liblz4-dev libssl-dev libxml2-dev libxslt1-dev pkg-config uuid-dev zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*
RUN curl -fsSLo /postgresql.tar.bz2 "https://ftp.postgresql.org/pub/source/v${PG_VERSION}/postgresql-${PG_VERSION}.tar.bz2" \
    && echo "${PG_SHA256}  /postgresql.tar.bz2" | sha256sum -c - \
    && mkdir /src && tar -xjf /postgresql.tar.bz2 -C /src --strip-components=1
WORKDIR /src
RUN ./configure --prefix=/opt/bitnami/postgresql --with-icu --with-openssl --with-ldap --with-libxml --with-libxslt \
       --with-uuid=e2fs --with-lz4 --with-libedit-preferred --disable-nls CFLAGS=-O2 \
       LDFLAGS="-Wl,-rpath=/opt/bitnami/common/lib -L/opt/bitnami/common/lib -Wl,-rpath=/opt/bitnami/postgresql/lib -L/opt/bitnami/postgresql/lib -Wl,--as-needed" \
    && make -j"$(nproc)" world-bin \
    && make install-world-bin DESTDIR=/out \
    && cp COPYRIGHT /out/COPYRIGHT

FROM docker.io/bitnamilegacy/postgresql:16.4.0-debian-12-r28@sha256:3ba6e6f11388778a8f9e862990833987fe477fb3e1d303407a31e7118e76354a
USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
COPY --from=build /out/opt/bitnami/postgresql/ /opt/bitnami/postgresql/
COPY --from=build /out/COPYRIGHT /opt/bitnami/postgresql/licenses/postgresql-16.15.0.txt
RUN rm /opt/bitnami/postgresql/licenses/postgresql-16.4.0.txt \
    && sed -i -e 's/16\.4\.0-20/16.15.0-0/g' -e 's/16\.4\.0/16.15.0/g' /opt/bitnami/postgresql/.spdx-postgresql.spdx \
    && sed -i 's/16\.4\.0-20/16.15.0-0/' /opt/bitnami/.bitnami_components.json \
    && postgres --version | grep -q ' 16\.15$'
# OpenHands never creates PostGIS; drop it and GDAL, which only PostGIS and the gdal/ogr tools load.
RUN cd /opt/bitnami/postgresql \
    && rm -r lib/gdalplugins lib/cmake/gdal share/gdal share/bash-completion share/contrib/postgis-3.4 \
    && rm lib/libgdal.so* lib/postgis-3.so lib/postgis_raster-3.so lib/postgis_topology-3.so lib/address_standardizer-3.so \
       lib/pkgconfig/gdal.pc share/extension/postgis* share/extension/address_standardizer* \
       include/gdal* include/ogr* include/cpl_* include/gnm* licenses/gdal-3.10.0.txt licenses/postgis-3.4.3.txt \
       bin/gdal* bin/ogr* bin/gnm* bin/nearblack bin/sozip bin/postgis bin/postgis_restore \
       bin/raster2pgsql bin/shp2pgsql bin/pgsql2shp bin/pgtopo_export bin/pgtopo_import \
    && perl -MJSON::PP -0777 -i -pe '$j = JSON::PP->new->pretty; $d = $j->decode($_); my %drop = map { $_ => 1 } qw(SPDXRef-gdal SPDXRef-postgis); $d->{packages} = [grep { !$drop{$_->{SPDXID}} } @{$d->{packages}}]; $d->{relationships} = [grep { !$drop{$_->{relatedSpdxElement}} } @{$d->{relationships}}]; $_ = $j->encode($d)' .spdx-postgresql.spdx \
    && ! grep -E '"(gdal|postgis)"' .spdx-postgresql.spdx \
    && ! find . \( -iname '*postgis*' -o -iname '*gdal*' -o -iname 'ogr*' \) | grep .
ENV APP_VERSION=16.15.0
USER 1001
