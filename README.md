# fava-dashboards

Minimal Docker image running [Fava](https://beancount.github.io/fava/) with the
[fava-dashboards](https://github.com/andreasgerstmayr/fava-dashboards) extension
pre-installed (plus `beanquery` for custom queries).

Built for the `kenny000666-apps` homelab stack as a replacement for the previous
`lazy-beancount` deployment for the company accounting ledger.

## Image

```
ghcr.io/kenny000666-apps/fava-dashboards:<semver>
```

Multi-arch: `linux/amd64`, `linux/arm64`.

## Installed components

| Package | Version |
|---------|---------|
| fava | 1.30.16 (>= 1.30.12) |
| fava-dashboards | 2.0.2 |
| beanquery | 0.2.0 |

Base image: `python:3.12-slim`. Fava listens on port **5000** (`0.0.0.0`).

## Run

Mount your Beancount ledger and pass the file path as the container argument:

```bash
docker run --rm -p 5000:5000 \
  -v "$PWD/ledger:/data:ro" \
  ghcr.io/kenny000666-apps/fava-dashboards:v1.0.0 \
  /data/main.beancount
```

The image has `ENTRYPOINT ["fava"]` with default host/port flags, and also sets
`FAVA_HOST=0.0.0.0` / `FAVA_PORT=5000` env vars, so overriding the arguments
with just the ledger path still listens on `0.0.0.0:5000`.

To enable the dashboards extension, add to your ledger's Fava options:

```beancount
2000-01-01 custom "fava-option" "extensions" "fava_dashboards"
```

Dashboards are configured via a `dashboards.yml` next to the ledger — see the
[fava-dashboards README](https://github.com/andreasgerstmayr/fava-dashboards)
for the schema.

## Build pipeline

GitHub Actions (`.github/workflows/build.yml`) builds and pushes to GHCR on:

- push of a `v*` tag -> plain semver tag (e.g. `v1.0.0`)
- push to `main` -> prerelease tag `v0.0.0-<sha>-<timestamp>`
