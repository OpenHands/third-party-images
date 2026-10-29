# syntax=docker/dockerfile:1
FROM ghcr.io/lmnr-ai/frontend-ee:0.1.825@sha256:6724e5f7a633b3932671f8a255a42139f4c0731d1f860196beeffe3e5a4ab534
USER root
RUN apk upgrade --no-cache
# Node 26.12.1 is the 2026-10-21 security release; same Alpine 3.24 as upstream.
COPY --from=node:26.12.1-alpine@sha256:40cd011b55415bc894e45b02fdd5a5dfde056921b7d23a64c74f80497af48797 /usr/local/bin/node /usr/local/bin/node
RUN node --version | grep -qx v26.12.1
USER nextjs
