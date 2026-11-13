# crw-camoufox

Automated build of [fastcrw/crw](https://github.com/fastcrw/crw) (AGPL-3.0)
**with the `camoufox` renderer feature** — the stealth Camoufox failover tier
that the vendor's published image does not include (it builds
`--features cdp,impersonated` only).

A GitHub Actions workflow in this repo runs daily: if fastcrw has published a
new release, the workflow compiles it on GitHub's runners with
`--features cdp,impersonated,camoufox` (via the vendor's own `CARGO_PKGS`
build-arg) and publishes the result to this repo's GHCR package. Quiet days
exit in seconds.

## Pulling the image

Requires `docker login ghcr.io` with a token that has read-packages access to
this repo (a fine-grained PAT scoped to this repo works).

```
ghcr.io/<owner>/crw-camoufox:latest        # newest build
ghcr.io/<owner>/crw-camoufox:<upstream-tag> # pinned, e.g. 0.44.0
```

## Using the camoufox tier

The image alone is not enough — the tier talks to a
[camofox-browser](https://github.com/jo-inc/camofox-browser) sidecar over
REST. Add to your crw config TOML (shared Docker network required so the
container name resolves):

```toml
[renderer.camoufox]
base_url = "http://camofox-browser:9377"
include_in_auto = true
```

Verify with `GET /v1/capabilities` (camoufox should appear in the renderer
list) and a pinned scrape (`"renderer": "camoufox"`).
