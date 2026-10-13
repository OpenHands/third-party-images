# syntax=docker/dockerfile:1
# Source is the OpenHands fork (upstream 2cc50b0 plus dependency CVE bumps). Keep the
# binary at /generic-device-plugin: the OpenHands chart passes only args.
FROM --platform=$BUILDPLATFORM docker.io/library/golang:1.26.9-bookworm@sha256:d9c68c2c51161e12fd77e4c6320687c9cd86e1af1e3ad6e6cd63ff970641453c AS build
ARG TARGETOS TARGETARCH
ADD --keep-git-dir=false https://github.com/OpenHands/generic-device-plugin.git#3cda88b312ce7588093a04793a27a09509b34917 /src
WORKDIR /src
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -mod=vendor -trimpath \
      -ldflags "-s -w -X github.com/squat/generic-device-plugin/version.Version=2cc50b0-oh1" \
      -o /out/generic-device-plugin . \
    # Apache-2.0 §4(d) requires shipping the compiled-in dependencies' NOTICE files.
    && mkdir -p /out/licenses \
    && cp LICENSE /out/licenses/LICENSE \
    && for f in $(find vendor -name NOTICE | sort); do \
         printf '==> %s <==\n' "$(dirname "${f#vendor/}")"; cat "$f"; printf '\n'; \
       done > /out/licenses/THIRD_PARTY_NOTICES

FROM scratch
COPY --from=build /out/licenses /licenses
COPY --from=build /out/generic-device-plugin /generic-device-plugin
ENTRYPOINT ["/generic-device-plugin"]
