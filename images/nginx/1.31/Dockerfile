# syntax=docker/dockerfile:1
FROM docker.io/library/nginx:1.31.8@sha256:6e67f8eaac94fc456588d4ff6c470d984841f5b5dc4279863d5cbe6ec5a60def
# Holds nginx at 1.31.8 while Debian packages update.
RUN dpkg-query -W -f '${db:Status-Abbrev} ${Package}\n' 'nginx*' | awk '$1 == "ii" {print $2}' | xargs apt-mark hold \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade \
    && apt-mark unhold $(apt-mark showhold) \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && nginx -v 2>&1 | grep -q 'nginx/1\.31\.8$'
