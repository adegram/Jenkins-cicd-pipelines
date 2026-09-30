# Jenkins Kubernetes CD Pipeline

This project demonstrates a Jenkins pipeline for deploying a containerized application to a Kubernetes production environment.

The pipeline validates Kubernetes manifests, enforces immutable container images, requires production approval, and verifies the deployment rollout.

## Pipeline Overview

* Checks out the source code from Git.
* Renders the Kubernetes manifests using Kustomize.
* Verifies that the rendered manifest is not empty.
* Requires production deployments to use an immutable image pinned to a SHA256 digest.
* Requires manual approval before deploying to production.
* Uses a Jenkins-managed kubeconfig credential to access the production cluster.
* Applies the Kubernetes manifests to the production cluster.
* Waits for the application deployment to complete successfully.
* Archives the rendered Kubernetes manifest for reference.
* Prevents concurrent pipeline executions.
* Automatically stops the pipeline if it exceeds the 20-minute timeout.

## Production Image Requirement

Production deployments must provide an image using a SHA256 digest.

Example:

```text
registry.example/app@sha256:0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
```

This prevents production deployments from relying on mutable image tags such as `latest`.

## Jenkins Configuration

The pipeline expects:

* A Jenkins agent with the `kubectl` label.
* `kubectl` installed and configured on the Jenkins agent.
* A Jenkins file credential named `kubeconfig-prod`.
* A Jenkins user/group named `release-managers` for production approval.
* Kubernetes manifests located in `Jenkins-cd-k8s/manifests`.

## Kubernetes Manifests

The deployment manifest uses an image placeholder:

```yaml
image: IMAGE_PLACEHOLDER
```

During deployment, Jenkins replaces `IMAGE_PLACEHOLDER` with the image supplied through the `IMAGE` build parameter.

## Deployment

The pipeline is triggered with an `IMAGE` parameter.

Example:

```text
IMAGE=registry.example/app@sha256:<digest>
```

When running on the `main` branch:

1. Manifests are validated.
2. The image digest is validated.
3. A release manager must approve the deployment.
4. Jenkins applies the manifests.
5. Jenkins waits for the Kubernetes deployment rollout.

## Rollback

If a deployment needs to be rolled back, Kubernetes can be used to inspect the deployment history and revert to a previous revision:

```bash
kubectl rollout history deployment/app -n production
kubectl rollout undo deployment/app -n production
kubectl rollout status deployment/app -n production
```

## Technologies

* Jenkins
* Kubernetes
* Kustomize
* kubectl
* Git
* Container images
