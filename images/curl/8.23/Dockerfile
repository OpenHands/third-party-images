# syntax=docker/dockerfile:1
FROM docker.io/curlimages/curl:8.23.0@sha256:11e64e28c5f58c8d93ee95fcf22ac055f48b0bc440e0ce7d8013bbb5ad6aa8ac
USER root
RUN apk upgrade --no-cache \
    && curl --version | grep -q '^curl 8\.23\.0 '
USER curl_user
