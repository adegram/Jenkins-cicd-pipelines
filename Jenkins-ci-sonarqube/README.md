# Jenkins SonarQube Code Quality Pipeline

Jenkins pipeline for running application tests, generating code coverage, analyzing the source with SonarQube, and enforcing the configured quality gate.

## Overview

This pipeline provides a CI quality-validation workflow:

- Checks out the Jenkins pipeline repository.
- Checks out the application repository and selected Git ref.
- Installs dependencies with `npm ci`.
- Runs linting, tests, and test coverage.
- Runs SonarQube static analysis.
- Publishes JavaScript/LCOV coverage to SonarQube.
- Waits for the configured SonarQube quality gate.
- Archives coverage reports for build inspection.

## Pipeline Flow

```text
Checkout Pipeline
       ↓
Checkout Application
       ↓
Install / Lint / Test / Coverage
       ↓
SonarQube Analysis
       ↓
Quality Gate
```

## Repository Structure

```text
.
├── Jenkinsfile
└── application/
    └── services/
        └── api-gateway/
```

The application repository is checked out into the `application/` directory at runtime.

## Build Parameters

| Parameter | Purpose | Default |
|---|---|---|
| `APP_REPOSITORY` | Application repository to analyze | `https://github.com/adegram/ministore-microservices.git` |
| `APP_REF` | Branch or tag to analyze | `main` |
| `SONAR_PROJECT_KEY` | SonarQube project key | `portfolio-service` |

These parameters allow the same pipeline to analyze different application refs or repositories without modifying the Jenkinsfile.

## Application Validation

The pipeline runs from:

```text
application/services/api-gateway
```

The following commands are executed:

```bash
npm ci
npm run lint --if-present
npm test --if-present
npm run test:coverage --if-present
```

`npm ci` provides a clean, lockfile-based dependency installation. Linting, tests, and coverage are executed when the corresponding npm scripts are defined.

## SonarQube Analysis

The pipeline uses the Jenkins SonarQube installation named:

```text
sonarqube
```

Analysis is configured to:

- Scan the application source.
- Exclude `node_modules` and `coverage`.
- Import coverage from `coverage/lcov.info`.
- Associate the analysis with the current SCM revision.

## Quality Gate

After analysis, Jenkins waits for the SonarQube quality gate:

```text
SonarQube Analysis
        ↓
Quality Gate
        ↓
PASS → Pipeline continues
FAIL → Pipeline aborts
```

The quality gate is given a maximum of 5 minutes to return a result.

A failed quality gate prevents the pipeline from completing successfully.

## Requirements

The Jenkins agent must provide:

- Jenkins
- Git
- Node.js 24
- npm
- SonarScanner

The Jenkins agent used by the pipeline must have the labels:

```text
node24
sonar-scanner
```

Jenkins must also have a SonarQube installation configured with the name:

```text
sonarqube
```

## Build Artifacts

Coverage output is archived after every build:

```text
application/services/api-gateway/coverage/**
```

This preserves coverage data for troubleshooting and build inspection.


## Failure Handling

A failure during dependency installation, linting, testing, SonarQube analysis, or the quality gate causes the pipeline to fail.

The application should not be considered releasable until the configured tests and SonarQube quality gate pass.
