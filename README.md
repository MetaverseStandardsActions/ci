# Metaverse Standards Forum continuous integration scripts

## Purpose

This repository contains commonly used CI scripts for the Metaverse Standards Forum infrastructure.

### Contents

### For concept datasets

The following workflows are provided:

`glossary-build.yml`:: used for building and releasing a concept dataset.

`glossary-trigger.yml`:: used to trigger Metaverse Standards Glossary site deployments from a concept dataset.


Example 1. Used in the Metaverse Standards Forum Glossary concepts dataset

```yaml
name: build

on:
push:
branches:
- main
- staging
tags:
- 'v*'
pull_request:
workflow_dispatch:

jobs:
build-release:
name: Build and release
uses: metaversestandardsactions/ci/.github/workflows/glossary-build.yml@main

trigger-deploy:
name: Trigger downstream repositories
needs: build-release
uses: metaversestandardsactions/ci/.github/workflows/glossary-trigger.yml@main
with:
repository: metaversestandardsactions/glossary.metaverse-standards.org
secrets:
token: ${{ secrets.GLOSSARIST_CI_PAT_TOKEN }}
```
### For Metaverse Standards Forum sites

The following workflows are provided:

`site-deploy.yml`:: used to provide a build and deploy workflow for a Geolexica site.

Example 2. Used in the ISO/TC 211 Geolexica site
```yaml
name: deploy

on:
push:
branches:
- main
- staging
pull_request:
repository_dispatch:
types:
- deploy

jobs:
build-deploy:
uses: geolexica/ci/.github/workflows/site-deploy.yml@main
with:
repository: geolexica/isotc211-glossary
concepts-path: isotc211-glossary
environment-name: ${{ github.ref == 'refs/heads/main' && 'production' || 'staging' }}
environment-url: ${{ github.ref == 'refs/heads/main' && 'https://isotc211.geolexica.org' || 'https://isotc211-staging.geolexica.org' }}
production-branch: refs/heads/main
staging-branch: staging
secrets:
aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
aws-region: ${{ secrets.AWS_REGION }}
aws-cf-distribution-id: ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }}
aws-s3-bucket-name: ${{ secrets.S3_BUCKET_NAME }}
```

## License
Apache-2.0

2023 Copyright Ribose
2023 Copyright The Metaverse Standards Forum, Inc.
