# HS-CTF skeleton structure

Create separate folders for each challenge category, and add the category names to `ctfup.yml`. This deployment model is cannibalized from (csivitu/ctfup)[]. And uses the same challenge.yml file as in (ctfcli)[], with the following added fields for deploying to kubernetes. `exposeHttp` creates an ingress, while `exposeTCP` exposes the challenge with a loadbalancer, and uses ExternalDNS to create an A-record for the loadbalancer IP and hostname. Remember to update the container registry address in `ctfup.yml` as well.

Deploying a web server with an ingress:
```
replicas: 2
containers:
  server:
    build: .
    ports:
    - containerPort: 80
exposeHttp:
- containerPort: 80
host: chal1.heltsikker.no
```
Deploying a TCP/UDP challenge:
```
replicas: 2
containers:
  server:
    build: .
    ports:
    - containerPort: 80
exposeTCP:
- containerPort: 80
host: chal1.heltsikker.no
```

For other challenges (IE not in K8s), we can just omit these fields, as in the standard ctfcli `challenge.yml`:

```
# This file represents the base specification of your challenge. It is used by
# other tools to install and deploy your challenge.

# Required sections
name: "{{cookiecutter.name}}"
author: "author"
category: category
description: This is a sample description
value: 100
type: standard

# Settings used for Dockerfile deployment
# If not used, remove or set to null
# If you have a Dockerfile set to .
# If you have an imaged hosted on Docker set to the image url (e.g. python/3.8:latest, registry.gitlab.com/python/3.8:latest)
# Follow Docker best practices and assign a tag
image: null

# Optional settings
# Can be removed if unused
attempts: 5

# Flags specify answers that your challenge use. You should generally provide
# at least one.
# Can be removed if unused
# Accepts strings or dictionaries
flags:
    - flag{3xampl3}
    - {
        type: "static",
        content: "flag{wat}",
        data: "asdfasdfsdf",
    }

# Tags are used to classify your challenge with topics. You should provide at
# least one.
# Can be removed if unused
# Accepts strings
tags:
    - web
    - sandbox
    - js

# Provide paths to files from the same directory that this file is in
# Accepts strings
files:
    - dist/source.py

# Hints are used to give players a way to buy or have suggestions. They are not
# required but can be nice.
# Can be removed if unused
# Accepts dictionaries or strings
hints:
    - {
        content: "This hint costs points",
        cost: 10
    }
    - This hint is free

# Requirements are used to make a challenge require another challenge to be
# solved before being available.
# Can be removed if unused
# Accepts challenge names as strings or challenge IDs as integers
requirements:
    - "Warmup"
    - "Are you alive"
    - 1

# The state of the challenge.
# This is visible by default. It takes two values: hidden, visible.
state: hidden

# Specifies what version of the challenge specification was used.
# Subject to change until ctfcli v1.0.0
version: "0.1"
```

## Config
The workflows requires the following secrets to be set in order to deploy challenges:  

- TFD_TOKEN: CTFd admin token
- CTFD_URL: CTFd url: eg. https://ctf.heltsikker.no
- GKE_CLUSTER_NAME: Name of K8S cluster
- GKE_CLUSTER_ZONE: The K8S cluster zone
- GKE_PROJECT: GCP cluster ID
- GKE_SA_KEY: Service account key json file
