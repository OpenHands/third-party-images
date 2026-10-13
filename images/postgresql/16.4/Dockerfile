# syntax=docker/dockerfile:1
FROM docker.io/bitnamilegacy/postgresql:16.4.0-debian-12-r28@sha256:3ba6e6f11388778a8f9e862990833987fe477fb3e1d303407a31e7118e76354a
USER root
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
USER 1001
