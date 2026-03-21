# Self-Hosted chrx Setup

## Overview

This fork supports self-hosted chrx distribution via the `CHRX_WEB_ROOT`
environment variable. Instead of fetching from `chrx.org`, Chromebooks
pull the bootstrap tarball from a local HTTP server (e.g. imgbin on
CPhHex).

## Building the distribution tarball

```bash
# From the chrx repo root:
./make-dist.sh
```

This produces `dist.tar.gz` containing:
- `bin/chrx`, `bin/chrx-install`, `bin/chrx-setup-storage`
- `etc/chrx-devices`
- `etc/chrx-files/` (distro configs)

The version is auto-set to `<CHRX_VERSION>-dev` unless you override:
```bash
CHRX_VERSION="3.0.2" ./make-dist.sh
```

## Hosting on imgbin (CPhHex)

Upload the tarball and bootstrap scripts to the imgbin web root:

```bash
# Files needed on the web server:
#   /chrx/dist.tar.gz   — the tarball from make-dist.sh
#   /chrx/chrx           — the chrx main script (for curl|bash bootstrap)
#   /chrx/go             — the go bootstrap script

scp dist.tar.gz chrx go \
  user@cphhex:/path/to/imgbin/chrx/
```

Current hosting: `https://imgbin.office.computerphreaks.xyz/chrx/`

## Installing on a Chromebook (ChromeOS VT-2 shell)

From a ChromeOS crosh/VT-2 shell (Ctrl+Alt+F2, login as `chronos`):

```bash
cd /tmp
export CHRX_WEB_ROOT="https://imgbin.office.computerphreaks.xyz/chrx"
curl -k $CHRX_WEB_ROOT/dist.tar.gz \
  | sudo tar xzfC - /usr/local && chrx -d ubuntu
```

### Notes
- `-k` is needed if the imgbin cert is self-signed or not trusted by ChromeOS
- `CHRX_WEB_ROOT` must NOT have a trailing slash
- `/usr/local` is used because `/home/chronos` has noexec mount restrictions
- Default distro is GalliumOS (dead) — always pass `-d ubuntu` or your
  preferred distro explicitly

## Local testing (on the dev machine)

Use the included helper to spin up a local HTTP server and test:

```bash
./fetch-install-from-local.sh
```

This starts `python3 -m http.server`, fetches the tarball, and runs
`chrx -h` to verify the install.

## Repository locations

| Remote  | URL |
|---------|-----|
| origin  | https://github.com/reynhout/chrx (upstream) |
| dragon788 | git@github-dragon788:dragon788/chrx.git (GitHub fork) |
| gitea   | https://git.office.computerphreaks.xyz/ewscph/chrx.git |

Active branch: `claude/fix-grub-efi-mount-1eAUZ`
