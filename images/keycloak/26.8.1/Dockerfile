# syntax=docker/dockerfile:1
# Upstream Keycloak 26.8.1 inside the Bitnami 26.3.0 layout, so the Bitnami Keycloak chart can run it.
FROM docker.io/bitnamilegacy/keycloak:26.3.0-debian-12-r0@sha256:f5727c6357f947beda242391a9c6add244b50d5b179687e6c6d1a919f9f5293e AS legacy
FROM quay.io/keycloak/keycloak:26.8.1@sha256:a78d6b79572168cb25886b80a8b5b8d4ed4390743e3eecb810538683e209dd6b AS keycloak
FROM docker.io/library/eclipse-temurin:21.0.12.1_1-jre-jammy@sha256:affcab5356e1270ac35d2361ee83135af2f03e8d580eca78a6d5d6fdbf048db0 AS java

FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.10-bookworm@sha256:632dff7351799a5a9954a48dc06670ade72ee44e8c351cdc0e5a57af2fea21af AS wait-for-port
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch v1.0.11 https://github.com/bitnami/wait-for-port /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-s -w" -o /out/wait-for-port .

FROM legacy
USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /opt/bitnami/java /opt/bitnami/keycloak \
    && rm -f /opt/bitnami/common/.spdx-wait-for-port.*

COPY --from=java /opt/java/openjdk/ /opt/bitnami/java/
COPY --from=keycloak --chown=1001:0 /opt/keycloak/ /opt/bitnami/keycloak/
COPY --from=wait-for-port /out/wait-for-port /opt/bitnami/common/bin/wait-for-port
# The Bitnami chart's metrics configuration expects this provider.
COPY --from=legacy --chown=1001:0 /opt/bitnami/keycloak/providers/keycloak-metrics-spi-7.0.0.jar /opt/bitnami/keycloak/providers/

# The chart mounts an empty conf directory and copies these writable trees.
RUN cp -a /opt/bitnami/keycloak/conf /opt/bitnami/keycloak/conf.default \
    && mkdir -p /opt/bitnami/keycloak/data /opt/bitnami/keycloak/providers/log \
                /opt/bitnami/keycloak/providers/tmp /bitnami/keycloak \
    && chown -R 1001:0 /opt/bitnami/keycloak /bitnami/keycloak \
    && chmod -R g=u /opt/bitnami/keycloak /bitnami/keycloak \
    && sed -i -e 's/26\.3\.0-0/26.8.1-0/' \
              -e 's/21\.0\.7-9-0/21.0.12.1-1-0/' \
              -e 's/1\.0\.9-0/1.0.11-0/' /opt/bitnami/.bitnami_components.json

ENV APP_VERSION=26.8.1
USER 1001
