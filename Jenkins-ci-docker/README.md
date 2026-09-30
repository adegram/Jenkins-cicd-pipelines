# Jenkins CI/CD Pipeline

Jenkins pipeline for building, scanning, and publishing the `api-gateway` container image from the [Ministore Microservices](https://github.com/adegram/ministore-microservices) application.

## Overview

This pipeline provides a controlled container build workflow:

- Checks out the Jenkins pipeline repository.
- Checks out the application source from `main`.
- Installs dependencies and runs linting and tests.
- Builds the Docker image using a commit-based tag.
- Scans the image with Trivy for HIGH and CRITICAL vulnerabilities.
- Publishes the image to Docker Hub from `main`.
- Cleans up credentials, images, and workspace data after each build.

## Pipeline Flow

```text
Checkout Pipeline Repository
            ↓
Checkout Application Source
            ↓
Install Dependencies / Lint / Test
            ↓
Build Docker Image
            ↓
Trivy Image Scan
            ↓
Publish to Docker Hub (main only)
```

## Repository Structure

```text
.
├── Jenkinsfile
└── application/
    └── services/
        └── api-gateway/
```

The application source is checked out into the Jenkins workspace at runtime.

## Image Build

The image repository is defined as:

```text
adehorizon/api-gateway
```

The image tag is derived from the application Git commit:

```text
<12-character-commit-sha>
```

If the commit SHA is unavailable, the Jenkins build number is used:

```text
build-<BUILD_NUMBER>
```

Example:

```text
adehorizon/api-gateway:abc123def456
```

## Security

The pipeline includes:

- `npm ci` for reproducible dependency installation.
- Trivy scanning with HIGH and CRITICAL severity thresholds.
- Docker Hub credentials managed through Jenkins Credentials.
- Non-interactive Docker authentication using `--password-stdin`.
- A workspace-scoped Docker configuration directory.
- Post-build cleanup of Docker credentials, images, and workspace data.

## Jenkins Credentials

The pipeline expects the following Jenkins credential:

```text
dockerhub-id
```

The credential should be configured as a Jenkins **Username with password** credential and is used only during image publication.

## Requirements

The Jenkins agent must provide:

- Jenkins
- Git
- Docker
- Node.js 24
- npm
- Trivy

The Jenkins agent used by the pipeline must have the labels:

```text
docker
node24
```

## Publication

Images are published only when the pipeline runs against:

```text
main
```

Feature or non-main branches still run the checkout, dependency, test, build, and image-scan stages but do not push the image to Docker Hub.

## Cleanup

After every build, the pipeline:

```text
docker logout
        ↓
Remove local image
        ↓
Delete Jenkins workspace
```

This keeps registry session data, local images, and workspace contents from persisting between builds.

## Failure Handling

A failed lint, test, image build, or Trivy scan stops the pipeline before publication.

If publication fails, inspect the relevant stage logs before retrying the build.
