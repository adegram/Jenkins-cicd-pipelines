# Jenkins CI Pipeline

This folder contains a Jenkins pipeline that performs Continuous Integration only. The pipeline handles the application build, Docker image creation, Docker Hub authentication, and image push.

## What the pipeline does

The Jenkinsfile runs the following stages:

1. **Clone Repository** – Pulls the `main` branch from GitHub.
2. **Build** – Enters the API Gateway directory and installs the Node.js dependencies.
3. **Docker Image Build** – Builds the API Gateway Docker image.
4. **Docker Login** – Authenticates with Docker Hub using credentials stored in Jenkins.
5. **Docker Tag and Push** – Tags the image and pushes it to Docker Hub.

## Tools Used

* Jenkins
* Jenkins Pipeline
* GitHub
* Node.js
* npm
* Docker
* Docker Hub

## Pipeline

```text
GitHub
   ↓
Clone Repository
   ↓
npm install
   ↓
Docker Build
   ↓
Docker Login
   ↓
Tag Image
   ↓
Push to Docker Hub
```

## Docker Image

The pipeline builds:

```bash
api-gateway:latest
```

It then tags and pushes the image to:

```bash
adehorizon/api-gateway:latest
```

## Jenkins Configuration

The pipeline uses Node.js `23.0.0`, configured through Jenkins:

```groovy
tools {
    nodejs 'NodeJS 23.0.0'
}
```

Docker Hub credentials are stored in Jenkins and accessed using the credential ID:

```text
dockerhub-id
```

The credentials are injected into the pipeline using Jenkins' `withCredentials` block rather than being written directly into the Jenkinsfile.

## Repository Structure

The pipeline expects the API Gateway to be located at:

```text
services/
└── api-gateway/
    ├── Dockerfile
    ├── package.json
    └── ...
```

## Jenkinsfile

The pipeline is written using Jenkins Declarative Pipeline syntax and can be run from a Jenkins Pipeline job.

## Next Steps

Some improvements I will be adding to other pipelines:

* Automated tests
* SonarQube code analysis
* Docker image vulnerability scanning
* Versioned Docker image tags
* GitHub webhook trigger
* Deployment to Kubernetes
* Argo CD for continuous deployment
