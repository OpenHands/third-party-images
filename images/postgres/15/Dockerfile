# syntax=docker/dockerfile:1
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.10-bookworm@sha256:632dff7351799a5a9954a48dc06670ade72ee44e8c351cdc0e5a57af2fea21af AS gosu
ARG TARGETOS TARGETARCH
RUN git clone --depth 1 --branch 1.19 https://github.com/tianon/gosu /src \
    && cd /src \
    && CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -trimpath -ldflags="-d -w" -o /out/gosu .

FROM docker.io/library/postgres:15.19@sha256:baa22e6286bf832cb147b8bb5aa9d8bc999846d69c21d11bf63f58fbeed1fa48
COPY --from=gosu /out/gosu /usr/local/bin/gosu
RUN gosu --version && gosu nobody true
