


# Rare2FAIR

**Version:** 2.0.0 &nbsp;|&nbsp; [Changelog](CHANGELOG.md)

Software to support FAIR Metadata and FAIR Data  authoring and publishing.


## High-level Architecture

R2F External should be deployed on a publicly-accessible server (e.g. in your Demilitarized Zone), and optimally has no access control - it acts like your institutional homepage, but for machines.

R2F Internal is an optional second component that includes software for executing data transformations compatible with the [Clinical and Registry Entries Semantic Model (CARE-SM)](https://github.com/CARE-SM/).  R2F Internal should be deployed inside of your firewall. **R2F External and R2F Internal are fully independent** - neither component calls, is called by, or shares any network, volume, or database with the other. Each is installed, secured, and operated on its own.


## CONTENTS

[Installing R2F External](./R2F/External-install/)

[Installing R2F Internal](./R2F/Internal-install/)

[Security & vulnerability transparency](./R2F/Security/VULNERABILITY_TRIAGE.md) -- every CRITICAL/HIGH finding from our image scans, and whether it's safe to deploy

