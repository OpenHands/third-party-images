# third-party-images

Rebuilds of third-party images that the OpenHands Helm charts deploy, at the same upstream versions, with OS packages and bundled Go helpers updated to clear fixable vulnerabilities.

Images publish to `ghcr.io/openhands/third-party-images/<name>:<tag>`. Each image keeps the last path segment of the image it replaces (`keycloak`, `postgresql`, `redis`, `postgres`, `rabbitmq`, `valkey`, `nginx`, `curl`, `app-server-ee`, `frontend-ee`, `frontend-ee-basepath`, `generic-device-plugin`), because Replicated airgap installs address images by that segment alone.

| Image | Replaces | What changes |
|---|---|---|
| `keycloak:26.3.0-debian-12-r0-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | Debian updates, `wait-for-port` rebuilt on current Go |
| `keycloak:26.7.5-debian-12-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | As above, plus upstream Keycloak 26.7.5 and Temurin 21.0.12 in the Bitnami layout. Upgrading migrates the Keycloak database one way |
| `keycloak:26.8.0-debian-12-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | As above with upstream Keycloak 26.8.0, the oldest Keycloak with no fixable critical or high findings. Upgrading migrates the Keycloak database one way |
| `postgresql:16.4.0-debian-12-r28-oh1` | `bitnamilegacy/postgresql:16.4.0-debian-12-r28` | Debian updates |
| `redis:7.4.3-debian-12-r0-oh1` | `bitnamilegacy/redis:7.4.3-debian-12-r0` | Debian updates, `wait-for-port` rebuilt on current Go |
| `postgres:15.19-oh1` | `postgres:15` (Laminar) | `gosu` rebuilt on current Go |
| `keycloak:26.8.0-debian-12-oh2` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | Same as `26.8.0-debian-12-oh1`, rebuilt for Debian updates and on current Go |
| `keycloak:26.8.1-debian-12-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | As `26.8.0-debian-12-oh1` with upstream Keycloak 26.8.1, which adds no database migration over 26.8.0 |
| `postgresql:16.15.0-debian-12-oh1` | `bitnamilegacy/postgresql:16.4.0-debian-12-r28` | Debian updates, PostgreSQL 16.15 built with Bitnami's configure options over the 16.4.0 image. Minor upgrade: same on-disk format, bundled extensions unchanged |
| `redis:7.4.12-debian-12-oh1` | `bitnamilegacy/redis:7.4.3-debian-12-r0` | Debian updates, `wait-for-port` rebuilt on current Go, Redis 7.4.12 built as Bitnami builds it (TLS, bundled jemalloc) over the 7.4.3 image |
| `postgres:15.19-oh2` | `postgres:15` (Laminar) | Same as `15.19-oh1` on a newer upstream digest and Go |
| `rabbitmq:4.4.0-oh1` | `rabbitmq:4.4.0` (Laminar) | Ubuntu updates, `gosu` rebuilt on current Go |
| `rabbitmq:4.4.0-oh2` | `rabbitmq:4.4.0` (Laminar) | As above, plus the base's `pebble` rebuilt at v1.33.0 on current Go |
| `valkey:9.1.2-alpine-oh1` | `valkey/valkey:9.1.2-alpine` (Laminar) | Alpine updates |
| `nginx:1.31.8-oh1` | `nginx:1.31.8` (Laminar) | Debian updates, nginx packages held at 1.31.8 |
| `curl:8.23.0-oh1` | `curlimages/curl:8.23.0` (Laminar) | Alpine updates |
| `app-server-ee:0.1.825-oh1` | `ghcr.io/lmnr-ai/app-server-ee:0.1.825` (Laminar) | Debian updates |
| `frontend-ee:0.1.825-oh1` | `ghcr.io/lmnr-ai/frontend-ee:0.1.825` (Laminar) | Alpine updates |
| `frontend-ee-basepath:0.1.825-oh1` | `ghcr.io/lmnr-ai/frontend-ee-basepath:0.1.825` (Laminar) | Alpine updates |
| `generic-device-plugin:2cc50b0-oh1` | `ghcr.io/squat/generic-device-plugin:2cc50b0` | Built from the [OpenHands fork](https://github.com/OpenHands/generic-device-plugin) on current Go, with `grpc`, `x/net` and `x/text` bumped |

## Building

`images.json` lists every image. A push to `main` that touches `images/`, `images.json` or the workflow builds each listed image for `linux/amd64` and `linux/arm64`. Tags are immutable: an existing tag is skipped, so a rebuild needs a new `-ohN` tag in `images.json`.
