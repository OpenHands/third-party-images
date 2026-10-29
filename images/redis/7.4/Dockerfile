# syntax=docker/dockerfile:1
# Redis 7.4.12 built as Bitnami builds it (TLS, bundled jemalloc), installed over the Bitnami 7.4.3 image's binaries.
FROM docker.io/library/debian:bookworm@sha256:854dd8ab51d62866dbd8f5431d15ce77b12983a2a4a1e1fe43a2395dc7ef9f61 AS redis
ARG REDIS_VERSION=7.4.12
ARG REDIS_SHA256=56588ee1648539556f2b6cf4e873e3734afbbe40fa952fec2fcd6b6d7dae2924
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       build-essential ca-certificates curl libssl-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*
RUN curl -fsSLo /redis.tar.gz "https://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz" \
    && echo "${REDIS_SHA256}  /redis.tar.gz" | sha256sum -c - \
    && mkdir /src && tar -xzf /redis.tar.gz -C /src --strip-components=1 \
    && make -C /src -j"$(nproc)" BUILD_TLS=yes USE_SYSTEMD=no \
    && make -C /src install PREFIX=/out \
    && cp /src/LICENSE.txt /out/LICENSE.txt

FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.10-bookworm@sha256:632dff7351799a5a9954a48dc06670ade72ee44e8c351cdc0e5a57af2fea21af AS wait-for-port
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch v1.0.11 https://github.com/bitnami/wait-for-port /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-s -w" -o /out/wait-for-port .

FROM docker.io/bitnamilegacy/redis:7.4.3-debian-12-r0@sha256:a25b5d07a14ec13730022c7cd9bab6308d55ccd86b74af7315553c17be884889
USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -f /opt/bitnami/common/.spdx-wait-for-port.*
COPY --from=wait-for-port /out/wait-for-port /opt/bitnami/common/bin/wait-for-port
# redis-check-aof, redis-check-rdb and redis-sentinel are existing symlinks to redis-server.
COPY --chmod=775 --from=redis /out/bin/redis-server /out/bin/redis-cli /out/bin/redis-benchmark /opt/bitnami/redis/bin/
COPY --from=redis /out/LICENSE.txt /opt/bitnami/redis/licenses/redis-7.4.12.txt
RUN rm /opt/bitnami/redis/licenses/redis-7.4.3.txt \
    && sed -i -e 's/7\.4\.3-0/7.4.12-0/g' -e 's/7\.4\.3/7.4.12/g' /opt/bitnami/redis/.spdx-redis.spdx \
    && sed -i 's/7\.4\.3-0/7.4.12-0/' /opt/bitnami/.bitnami_components.json \
    && redis-server --version | grep -q 'v=7\.4\.12 '
ENV APP_VERSION=7.4.12
USER 1001
