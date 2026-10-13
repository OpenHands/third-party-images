# third-party-images

Rebuilds of third-party images that the OpenHands Helm charts deploy, at the same upstream versions, with OS packages and bundled Go helpers updated to clear fixable vulnerabilities.

Images publish to `ghcr.io/openhands/third-party-images/<name>:<tag>`. Each image keeps the last path segment of the image it replaces (`keycloak`, `postgresql`, `redis`, `postgres`, `generic-device-plugin`), because Replicated airgap installs address images by that segment alone.

| Image | Replaces | What changes |
|---|---|---|
| `keycloak:26.3.0-debian-12-r0-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | Debian updates, `wait-for-port` rebuilt on current Go |
| `keycloak:26.7.5-debian-12-oh1` | `bitnamilegacy/keycloak:26.3.0-debian-12-r0` | As above, plus upstream Keycloak 26.7.5 and Temurin 21.0.12 in the Bitnami layout. Upgrading migrates the Keycloak database one way |
| `postgresql:16.4.0-debian-12-r28-oh1` | `bitnamilegacy/postgresql:16.4.0-debian-12-r28` | Debian updates |
| `redis:7.4.3-debian-12-r0-oh1` | `bitnamilegacy/redis:7.4.3-debian-12-r0` | Debian updates, `wait-for-port` rebuilt on current Go |
| `postgres:15.19-oh1` | `postgres:15` (Laminar) | `gosu` rebuilt on current Go |
| `generic-device-plugin:2cc50b0-oh1` | `ghcr.io/squat/generic-device-plugin:2cc50b0` | Built from the [OpenHands fork](https://github.com/OpenHands/generic-device-plugin) on current Go, with `grpc`, `x/net` and `x/text` bumped |

## Building

`images.json` lists every image. A push to `main` that touches `images/`, `images.json` or the workflow builds each listed image for `linux/amd64` and `linux/arm64`. Tags are immutable: an existing tag is skipped, so a rebuild needs a new `-ohN` tag in `images.json`.
