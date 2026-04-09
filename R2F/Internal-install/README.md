# R2F Internal Installation

R2F Internal is the FAIR data record component of the Rare2FAIR.  It can be run independently of R2F External, and is intended to be deployed on a separate network segment with appropriate security for handling (anonymized) clinical records.

It consists of 4 "dockreized" components, all of them _mandatory_, but with different requirements for activation.

- GraphDB - holds the RDF-formatted record data
- Transformation Daemon - this is the orchestrator for the RDF transformation
- Yarrrml-rdfizer - this executes the CSV to RDF transformation using the CARE-SM models
- CARE-SM - this does enrichment and quality control over the CSV data prior to transformation



## CONTENTS

- [Installation requirements](#requirements)
- [Downloading](#downloading)
- [Installing](#installing)
- [Quick Start - Data Transformations](#testing)

<a name="requirements"></a>

## Requirements

To use R2F Internal you `must` meet the following requirements.

**User requirements (Person who is deploying this solution)**

- Basic knowledge about Docker​
- Basic GitHub knowledge​

**System requirements​ (Machine where this solution is being deployed)**

- Docker engine ​
- Docker-compose application​

---

<a name="downloading"></a>

## Downloading

#### R2F Internal

To get R2F Internal clone this repository to your machine.

```sh
https://github.com/FAIR-Data-Systems/Rare2FAIR.git
```

---



## Installing

<a name="installing"></a>

## Preparing for Installation

At the beginning of the installation process you are asked three questions:

### A Prefix for your installation
The prefix is used as a "namespace" to isolate indepdent R2F installations from one another.  This allows you to run multiple CARE-SM Data servers on the same machine.  The prefix is used for the docker network, docker volumes, and appears in the configuration files and docker-compose yaml files.  This can be any set of letter/number characters.  Please do not use punctuation characters.  e.g. 'euronmd1'  We will use *'ACME'* for the remainder of this document.

### Port for your GraphDB
This is the port that will be used by the GraphDB database.  This is validated against a list of "banned" ports (ports that are likely to be used by other software on your system).  It is a good idea to stay in the range of ~4000-10000.  By detault, this port is disabled after installation, so your graphdb cannot be accessed.  You will need to enable it, at least once, to login to GraphDB's web page and create a secure user for your R2F Internal server (and change the login for the Admin user).  This port does NOT need to be enabled for the regular operation of R2F Internal, and should be disabled when not needed.

### Port for the transformation daemon
This is the port that will listen for requests to trigger a data transformation. It responds only to an "empty" HTTP GET request, does not allow any parameters, and does not process the HTTP request in any way.


## Installing R2F Internal

Once you have completed the "Downloading" section of this tutorial, and you have prepared your answers to the questions, cd into the `/Internal-install/` folder and run the instaler.

```
bash ./install-r2d-internal.sh
```

This script will bootstrap GraphDB.  It creates a databases in GraphDB called *ACME-r2d-internal*. This is the database that you will need to secure after installation.

### If you abort installation before it completes...

This can (and probably will) leave you in a state that needs some careful attention.  In particular, find any graph-db Docker Volumes *_that have your PREFIX_* and remove them `docker volume rm ACME-graph-db`.  If it will not delete, it will be due to the existence of a docker container that uses it.  You can safely delete this docker container also.  `docker rm AJDIRDjdsfhwe83hewfewkw5`.  Again, make sure you are deleting the right things!


### The folder with your final server configuration

The installer will create a sub-folder `ACME-R2d-Internal` underneath the folder where you ran your installation. This folder contains all of your server configuration files.  You can copy this folder anywhere on your system, e.g. to keep your servers all in one folder outside of your GitHub copy.  Inside that folder is a customized docker-compose file (docker-compose-ACME.yml) for your deployment.  So for example, you would issue the commands:

```

cp -r ACME-R2d-Internal ~/SERVERS/
cd ~/SERVERS/ACME-R2d-Internal
docker-compose -f docker-compose-ACME.yml up

```

Your R2F Internal server is now running at whatever port you selected.

## Securing your R2F Internal server

In principle, none of these components will have any internet-facing interfaces; nevertheless 
you should secure GraphDB.

These are the default login details and locations:

#### GraphDB

| Service name | Local deployment                                | Production deployment |
| ------------ | ----------------------------------------------- | --------------------- |
| GraphDB      | [http://localhost:7200](http://localhost:7200/) | SHOULD NOT BE VISIBLE |


| Username | Password |
| -------- | -------- |
| `admin`  | `root`   |

1.  Change the admin password
2.  Create a new user with read/write permissions on the *ACME-r2d-internal* database
3.  Ensure that secured access is switched ON




# CARE-SM R2F Internal Quick Start!
<a name="testing"></a>
---

**R2F Internal** is fully compatible with the **Clinical And Registry Entries Semantic Model (CARE-SM)**. This software implements a workflow that uses **CSV** and **YARRRML** templates to define the RDF shape and perform the transformation.

The only requirement is a **CSV template** that contains patient data based on CARE-SM. Once you have created and populated your CSV template(s), place the file(s) into the `R2d-Internal/data` folder. 

---

## How to Populate the CSV?

An example CSV data file called **`Diagnosis.csv`** is included in `R2d-Internal/data`. This file can be used as a default test option if you are unsure how to prepare your own template.  

If you want to use your own data, remove the `Diagnosis.csv` file and follow one of the options below:

1. **Map your own data:**  
   Check to the [CARE-SM Glossary documentation](https://care-sm.readthedocs.io/en/latest/glossary.html), which contains all the details needed for creating and populating your CSV template.

2. **Use predefined synthetic data:**  
   CARE-SM provides a set of synthetic CSV data tables for testing FiaB. You can find them [here](https://github.com/CARE-SM/CARE-SM-Implementation/tree/main/CSV/).

> **Note:** CSV filenames are **not flexible**. They are controlled by a specific vocabulary described in the [CARE-SM Glossary documentation](https://care-sm.readthedocs.io/en/latest/glossary.html).  

---

## Running the Transformation

Once your CSV data table is located in `R2d-Internal/data`, you can trigger the transformation by opening  `http://localhost:4567/` in your web browser. 

After a few seconds, your output RDF data will appear in the `R2d-Internal/data/triples` folder and will also be automatically uploaded into GraphDB’s **`cde`** database.