


# Rare2FAIR

Software to support FAIR Metadata and FAIR Data  authoring and publishing.


## High-level Architecture

R2F External should be deployed on a publicly-accessible server (e.g. in your Demilitarized Zone), and optimally has no access control - it acts like your institutional homepage, but for machines.

R2F Internal is an optional second component that includes software for executing data transformations compatible with the [Clinical and Registry Entries Semantic Model (CARE-SM)](https://github.com/CARE-SM/).  R2F Internal should be deployed inside of your firewall, and can only be accessed via tightly regulated calls from R2F External (see detailed documentation below).


## CONTENTS

[Installing R2F External](./R2F/External-install/)

[Installing R2F Internal](./R2F/Internal-install/)

