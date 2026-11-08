# Changelog

All notable changes to Rare2FAIR are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- The vulnerability register's `ACCEPT` decision is now called `NOT REACHABLE` (same meaning: the vulnerable
  code can't be reached in our deployment). "Accepted" reads as "known risk, tolerated", which it never
  meant. 29 rows relabelled, nothing else changed; the partner legend in `R2F/Security/VULNERABILITY_TRIAGE.md`
  now also says that the `ExposureTier` 3 rows rest on a judgment about the whole bundled component.

## [2.0.0] - 2026-11-06

Full migration onto the architecture Sextans Suite has been running since its own 2.0.0-3.2.0
releases, ported now that it's been tested there for several months. Every component image is
also freshly rebuilt/rescanned as of this release (`fairdatasystems/*:2026-11-06`).

### Changed

- **Internal: GraphDB → Virtuoso, CARE-SM v1 → CARE-SM-2.** `graph-db-repo-manager` (the bootstrap
  component that created a GraphDB repository via its REST API) is gone entirely — Virtuoso has no
  separate "repository" concept; isolation between installs comes from each running its own
  Virtuoso container/volume. `install-internal.sh` now prompts for and sets a Virtuoso `dba`
  password (minimum 12 characters) instead of GraphDB's separate security-enablement step, and a
  one-time SQL script (`virtuoso-initdb/lockdown-anonymous-sparql.sql`) locks down anonymous
  `/sparql` reads on first database creation. The transformation pipeline (`cde-box-daemon`,
  `yarrrml-rdfizer`, `caresm`) now runs the CARE-SM-2 Toolkit (images `cdeb2`/`care2`, up from the
  CARE-SM v1-based `cdeb`/`care`) — including CARE-SM-2's custom-datatype support
  (`R2F-internal/data/custom/<name>.csv` + matching YARRRML) and auto-pulled, smoke-tested upstream
  mapping updates on every transformation trigger. The two stale, committed
  `R2F-internal/data/CARE_yarrrml*.yaml` files are removed — the pipeline generates these at
  runtime now, they were never meant to be static repo content.
- **External: GraphDB → Virtuoso**, same architecture change as Internal, plus MongoDB
  authentication is now enabled by default (`MONGO_INITDB_ROOT_USERNAME`/`_PASSWORD`, a freshly
  generated password per install) and the FDP server's JWT signing secret is generated fresh per
  install (`openssl rand -hex 64`) rather than a fixed shared value.
- `FDP_Configs/ERDERA_Base/Dockerfile` now runs as a non-root `erdera` user (parity with Sextans
  Suite — this had fallen out of sync).

### Added

- **Published partner-facing vulnerability transparency documentation**, in a new `R2F/Security/`
  folder: `vulnerability-register.csv`, the backing per-image Trivy scan output, and a
  `VULNERABILITY_TRIAGE.md` explaining in plain terms how to read the `Decision`/`ExposureTier`
  columns for a deploy decision. Linked from the top-level README and the "Securing your server"
  section of both `Internal-install/README.md` and `External-install/README.md`. Mirrored from
  the same scan/triage process Sextans Suite publishes under its own `Security/` folder — this
  repo has no build/patch tooling of its own, just the published results, carried forward at each
  release.

### Fixed

- **`care2` (CARE-SM-2 Toolkit) shipped an unpatched Alpine OS layer** (1 CRITICAL + ~18 HIGH,
  all OS-layer — its Dockerfile never ran `apk upgrade`). Fixed upstream, rebuilt, rescanned
  clean, re-pushed under the same `fairdatasystems/care2:2026-11-06` tag used by this release — no
  compose file changes needed, since the tag didn't change.

- **`install-internal.sh`'s data-folder permissions now cover files added after install, not just
  what's present at install time.** `cde-box-daemon`/`yarrrml-rdfizer` run as a fixed container UID
  that generally won't match the host user, so the installer opens `R2F-internal/data` to any
  local user (`chmod -R o+rwX`) — previously a one-time step that never touched anything you added
  later (a new CSV, a `data/custom/` folder you create yourself), which could make a transformation
  fail against a brand-new file. The installer now also sets a default ACL (`setfacl`, when the
  `acl` package is available) so new files/folders inherit the same permissions automatically, with
  a printed warning and manual fallback when `setfacl` isn't available. Ported from the same fix in
  Sextans Suite ([wilkinsonlab/Sextans-Suite#5](https://github.com/wilkinsonlab/Sextans-Suite/issues/5)).

## [1.0.0] - 2026-07-10

### Added

- `FDP_Configs/ERDERA_Base/run-erdera-configuration.sh` — wrapper script for the ERDERA FDP configuration step, ported from Sextans Suite, so users no longer have to answer an interactive prompt that `docker compose` can't actually deliver keystrokes to.

### Fixed

- `Internal-install/install-internal.sh` and `External-install/install-external.sh` now auto-detect whether the host provides the `docker compose` plugin or the standalone `docker-compose` binary, and use whichever is available (parity with Sextans Suite).
- GraphDB bootstrap containers now bind to the port chosen during the install questionnaire (`GDB_PORT`) instead of a hardcoded `7200`, preventing a bind failure when GraphDB's default port is already in use on the host.
- `install-internal.sh` no longer leaves a stray temp compose file behind after bootstrapping (the cleanup line was a no-op string instead of an `rm` call).
- `FDP_Configs/ERDERA_Base/docker-compose.yml` now requires `FDP_PORT` via the environment instead of silently hanging on an interactive prompt.
- `FDP_Configs/ERDERA_Base/docker-compose.yml` referenced a nonexistent `markw/erdera-fdp-config:latest` tag; corrected to the actual published `:0.0.1` tag, matching Sextans Suite.

### Removed

- Dead `external: true` volume declarations in the production `docker-compose-template.yml` files for both Internal-install and External-install, referencing volumes no service in those files actually used.

### Docker Images

- `markw/erdera-fdp-config:0.0.1` — content unchanged this release; this image is shared verbatim with the Sextans Suite project, and both repos' Docker Compose files are kept pointing at the same tag.
