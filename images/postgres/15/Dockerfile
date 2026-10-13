# syntax=docker/dockerfile:1
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.9-bookworm@sha256:d9c68c2c51161e12fd77e4c6320687c9cd86e1af1e3ad6e6cd63ff970641453c AS gosu
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch 1.19 https://github.com/tianon/gosu /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-d -w" -o /out/gosu .

FROM docker.io/library/postgres:15.19@sha256:c961aa287d8698297cb26cdfadfbe9fd2cbaf77e53cfffe9636e8d8a1e4d842c
COPY --from=gosu /out/gosu /usr/local/bin/gosu
RUN gosu --version && gosu nobody true
