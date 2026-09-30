# Jenkins CI/CD Pipeline

Jenkins pipeline for validating Kubernetes manifests and deploying an immutable container image to a production Kubernetes cluster.

## Overview

This pipeline provides a controlled deployment workflow:

- Checks out the application repository.
- Renders Kubernetes manifests using Kustomize.
- Validates the rendered configuration.
- Requires production images to be pinned by SHA256 digest.
- Requires manual production approval from `release-managers`.
- Injects production Kubernetes credentials securely through Jenkins Credentials.
- Applies the rendered manifests to the production cluster.
- Waits for the Kubernetes Deployment rollout to complete.
- Archives the rendered manifest for troubleshooting and auditing.

## Pipeline Flow

```text
Checkout
   ↓
Render & Validate Manifests
   ↓
Require Immutable Image
   ↓
Production Approval
   ↓
Apply Kubernetes Manifests
   ↓
Verify Rollout
```

## Repository Structure

```text
.
├── Jenkinsfile
└── Jenkins-cd-k8s/
    └── manifests/
        ├── kustomization.yaml
        └── ...
```

## Deployment

The pipeline accepts an `IMAGE` parameter containing an immutable container image reference:

```text
registry.example/app@sha256:<digest>
```

Production deployments from `main` are rejected if the image is not pinned to a SHA256 digest.

The Kubernetes manifests use:

```text
IMAGE_PLACEHOLDER
```

The pipeline replaces this placeholder with the supplied image before applying the manifests.

## Production Controls

Production deployment is restricted to the `main` branch and includes:

- Immutable image enforcement.
- Manual deployment approval.
- Approval restricted to `release-managers`.
- Production kubeconfig stored in Jenkins Credentials.
- Kubernetes rollout verification with a 180-second timeout.
- Concurrent deployment prevention.

## Jenkins Credentials

The pipeline expects the following Jenkins credential:

```text
kubeconfig-prod
```

This credential should contain the production Kubernetes kubeconfig and is exposed to the deployment step through the `KUBECONFIG` environment variable.

No Kubernetes credentials are stored in the repository.

## Requirements

The Jenkins agent must have the required deployment tooling available, including:

- Jenkins
- Git
- Bash
- `kubectl`
- Kustomize support through `kubectl kustomize`

The Jenkins agent used by the pipeline must have the label:

```text
kubectl
```

## Rollback

The pipeline does not automatically perform rollbacks.

If a rollout fails, inspect the Kubernetes Deployment, Pods, events, and logs, then follow the cluster's established rollback procedure.

## Build Artifacts

The rendered Kubernetes manifest is archived as:

```text
rendered.yaml
```

This provides a record of the configuration generated during each build and can be used for troubleshooting and deployment auditing.