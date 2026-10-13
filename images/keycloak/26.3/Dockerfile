# syntax=docker/dockerfile:1
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.9-bookworm@sha256:d9c68c2c51161e12fd77e4c6320687c9cd86e1af1e3ad6e6cd63ff970641453c AS wait-for-port
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch v1.0.11 https://github.com/bitnami/wait-for-port /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-s -w" -o /out/wait-for-port .

FROM docker.io/bitnamilegacy/keycloak:26.3.0-debian-12-r0@sha256:f5727c6357f947beda242391a9c6add244b50d5b179687e6c6d1a919f9f5293e
USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -f /opt/bitnami/common/.spdx-wait-for-port.*
COPY --from=wait-for-port /out/wait-for-port /opt/bitnami/common/bin/wait-for-port
USER 1001
