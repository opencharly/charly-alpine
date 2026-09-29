# AGENTS.md — charly-alpine

This repo owns the **Alpine apk package repository for charly** — the artifact
(the published `.apk` packages + `APKINDEX` at
`opencharly.github.io/charly-alpine`) and the R10 bed (`check-alpine-repo`) that
proves the published repo installs. The `charly` binary itself is built in
`opencharly/charly`; this repo packages a released CalVer.

The candy here carries **no `skill:` entity**, so no per-repo corpus page is
projected; the owning guidance is the family skill `/charly-tools:charly`. The
gap is tracked in `opencharly/opencharly#291`.

Canonical files:

- `charly.yml` — the `check-alpine-repo` disposable bed plus its helper candies
  (`sudo-nopasswd`, `alpine-repo-tools`, `alpine-repo-box`).
- `.github/workflows/build.yml` — the manual package build, sign, install-test,
  and GitHub Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `charly.rsa.pub` — the apk repo signing key; `index.html` — the Pages landing page.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-tools:charly` — the owning (family) skill. The charly binary's
  per-distro package repos (`charly-{alpine,arch,fedora,ubuntu,debian}`), the
  `packaging:` metadata source, and the released-vs-in-development binary
  contract. Load before editing the packaging or the bed.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, `distro:` arms). Load before editing a
  helper candy.
- `/charly-check:check` — the check-bed reference (`disposable: true` beds,
  deploy-scope check authoring, `charly check run <bed>`). Load before editing
  the bed.
- There is no `/charly-distros:alpine` skill in the corpus (the Alpine distro
  vocabulary exists in charly's embedded build vocabulary, but no Alpine family
  skill is projected); a reader reasoning about the Alpine guest's apk/OpenRC
  path has `/charly-tools:charly` as the owning reference.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate**.
- The manifest validates with `charly box validate` at the repo root.
- The R10 bed is `charly check run check-alpine-repo` — it deploys the
  disposable Alpine pod and installs the packaged charly from the PUBLISHED
  repo. The artifact build/sign/install-test lives in
  `.github/workflows/build.yml` (manual dispatch with a release CalVer).

## Modify this repo

- Package builds are driven from the `opencharly/charly` release; this repo
  does not build the binary. A packaging-metadata change lands in the charly
  repo's `packaging:` section first.
- Keep the `check-alpine-repo` bed honest against the PUBLISHED repo — it is
  the R10 acceptance for the Alpine leg.
- New behaviour claims belong in the bed's `plan:` as an observable `check:`
  step.

## Landing

- The authoritative landing mechanics are `/charly-internals:git-workflow` and
  the umbrella `AGENTS.md` in `opencharly/opencharly`; this signpost does not
  restate them.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time).
