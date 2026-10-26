# syntax=docker/dockerfile:1
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.10-bookworm@sha256:632dff7351799a5a9954a48dc06670ade72ee44e8c351cdc0e5a57af2fea21af AS gosu
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch 1.19 https://github.com/tianon/gosu /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-d -w" -o /out/gosu .

# The Ubuntu base ships pebble built from an unpublished commit just after v1.33.0.
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.27.3-bookworm@sha256:972b4110ba0f47539a975c31e52c64765e1ff19f2d74469ae4a595c6676e80a5 AS pebble
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch v1.33.0 https://github.com/canonical/pebble /src \
    && cd /src \
    && ./cmd/mkversion.sh v1.33.0 \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -o /out/pebble ./cmd/pebble

FROM docker.io/library/rabbitmq:4.4.0@sha256:71be6ca71cd3ad92f29b354ec381b1ee587894aaaac21f185b8cf43fb50d3feb
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
# Replaces the Ubuntu gosu package's binary at the path the entrypoint uses.
COPY --from=gosu /out/gosu /usr/sbin/gosu
COPY --from=pebble /out/pebble /usr/bin/pebble
RUN gosu --version && gosu nobody true && pebble version --client
