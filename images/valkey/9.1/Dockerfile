# syntax=docker/dockerfile:1
FROM docker.io/valkey/valkey:9.1.2-alpine@sha256:48332870af354a799964c0012ae1194a0bf2bf894eb508f945810596dc2d8d11
RUN apk upgrade --no-cache \
    && valkey-server --version | grep -q 'v=9\.1\.2 '
