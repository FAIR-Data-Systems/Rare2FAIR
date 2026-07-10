# Changelog

All notable changes to Rare2FAIR are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

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
