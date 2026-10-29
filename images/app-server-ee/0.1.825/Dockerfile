# syntax=docker/dockerfile:1
FROM ghcr.io/lmnr-ai/app-server-ee:0.1.825@sha256:b9944f9a544d0bc8ddb4183df79a10340f99a01d905870fee459de0e201f0631
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
