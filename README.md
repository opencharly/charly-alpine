# charly-alpine

The Alpine Linux package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.apk` for `amd64` and `arm64`.

This repo owns the artifact **and the R10 bed that proves it**: the
`check-alpine-repo` deploy runs a disposable Alpine container pod, adds the
published apk repo, installs the packaged `charly`, and asserts the installed
binary's version equals the version the package manager recorded.

## Add the repository

```sh
wget -O /etc/apk/keys/charly.rsa.pub https://opencharly.github.io/charly-alpine/charly.rsa.pub
echo "https://opencharly.github.io/charly-alpine/amd64" >> /etc/apk/repositories
apk update
apk add charly
```

For `arm64` hosts, use `https://opencharly.github.io/charly-alpine/arm64` in `/etc/apk/repositories`.

## Direct install

Download the `.apk` for your architecture and install it with `apk add`:

- amd64: `https://opencharly.github.io/charly-alpine/amd64/charly-amd64.apk`
- arm64: `https://opencharly.github.io/charly-alpine/arm64/charly-arm64.apk`

## Variants

| Package | Plugin set |
|---|---|
| `charly` | secrets, feature, vm, doctor, clean, settings, candy, mcp, review, pipeline (10) |
| `charly-full` | the default set + udev, preempt (12) |
| `charly-minimal` | doctor, clean, settings (3) |

## Triggering a build

The build workflow is manual: **Actions → build → Run workflow**, entering the
charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release
is the source of truth for the binary, the plugins, and the packaging metadata.
Each build assembles the repo for both `amd64` and `arm64`, signs the packages
and the `APKINDEX`, and install-tests the result before deploying to GitHub
Pages.

## Verification

- **CI install-test** (inside the build workflow): installs `charly` from a
  local `file://` mount of the assembled repo, asserts `charly version` equals
  the packaged release, asserts every default-variant `plugin-<word>` is a
  symlink to the shared `charly-lib` host with its `.providers` manifest, and
  runs `charly doctor` from a non-project directory to prove the baked plugins
  dispatch project-less.
- **R10 bed** `check-alpine-repo`: `charly check run check-alpine-repo` deploys
  the disposable Alpine pod, installs the packaged `charly` from the PUBLISHED
  repo, and asserts the installed `/usr/bin/charly version` equals the version
  the package manager recorded.

## Layout

- `charly.yml` — the `check-alpine-repo` bed and its helper candies
  (`sudo-nopasswd`, `alpine-repo-tools`, `alpine-repo-box`).
- `.github/workflows/build.yml` — the manual package build + Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `charly.rsa.pub` — the apk repo signing key.
- `index.html` — the Pages landing page.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-tools:charly` — the charly binary and its per-distro package repos.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.
