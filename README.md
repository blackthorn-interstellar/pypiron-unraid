# pypiron-unraid

Unraid [Community Applications](https://ca.unraid.net) template for
[**pypiron**](https://github.com/blackthorn-interstellar/pypiron) — a self-hosted PyPI
server in a single binary, with no database. One container hosts your private uploads,
mirrors an allowlist of public packages, proxies the rest of PyPI on demand, and serves it
all from a standard simple index. Docs: [pypiron.com](https://pypiron.com).

This repo exists only to publish and maintain the Unraid listing, kept separate from the
source tree so Community Applications scans templates (not the Rust project) and listing
tweaks don't need a pypiron release.

## Layout

```
ca_profile.xml          # repository profile shown in Community Applications
LICENSE                 # MIT (mirrors pypiron)
icon.png                # 256×256 brand mark
templates/
  pypiron.xml           # the Docker container template
```

## What the template sets up

- **WebUI / index** on port `8080` — dashboard at `/`, simple index at `/simple/`.
- **Data** at `/data` (default `/mnt/user/appdata/pypiron`) — the package storage tree.
  This *is* your registry; back it up. Upload/proxy spooling lands here too
  (`PYPIRON_SPOOL_DIR=/data/spool`) so multi-GB wheels never spool into container RAM.
- **Admin Password** (`PYPIRON_ADMIN_PASS`) — set it to enable uploads + admin
  (publish/delete/yank/mirror); blank leaves a read-only public index.
- **Proxy Upstream** (`PYPIRON_PROXY_UPSTREAM`) — set to `https://pypi.org/simple/` to
  lazily proxy and cache PyPI; blank for a private-only index.
- Advanced: admin user, spool dir, and a Read User/Password pair to make reads private.

### Unraid specifics

The pypiron image is distroless — it runs as a fixed non-root uid and has **no PUID/PGID
and no shell**. The template runs the container as the standard appdata owner via
`--user 99:100` so it can write to `/data`; the binary does no passwd lookup, so an
arbitrary uid is fine. (That's also why there's no console/shell button.)

## Publishing to Community Applications

1. Push this repo to `github.com/blackthorn-interstellar/pypiron-unraid` on the `main`
   branch (the raw URLs in `ca_profile.xml` and `templates/pypiron.xml` assume that).
2. Submit the repo at [`ca.unraid.net/submit`](https://ca.unraid.net/submit) — the
   validator parses `ca_profile.xml` + the template, checks for duplicates, and previews
   the listing before publish.

Updating the listing later is just a push to this repo; CA re-scans.

## Before first submission

- [ ] Create the GitHub repo and push (`main` branch).
- [ ] Create an Unraid support forum thread and swap `<Support>` in `templates/pypiron.xml`
      from the GitHub issues URL to the forum topic (CA prefers a forum thread).
- [ ] Confirm `ghcr.io/blackthorn-interstellar/pypiron:latest` is public and multi-arch.
- [ ] Optionally add a `Screenshot` of the dashboard to the template.
- [ ] Run the validator at `ca.unraid.net/submit` and fix anything it flags.
