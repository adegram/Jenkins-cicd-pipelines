# Jenkins CI Pipeline

This folder contains a Jenkins pipeline that performs Continuous Integration only. The pipeline handles the application build, Docker image creation, Docker Hub authentication, and image push.

## What the pipeline does

The Jenkinsfile runs the following stages:

1. **Clone Repository** – Pulls the `main` branch from GitHub.
2. **Build** – Enters the API Gateway directory and installs the Node.js dependencies.
3. **Docker Image Build** – Builds the API Gateway Docker image.
4. **Docker Login** – Authenticates with Docker Hub using credentials stored in Jenkins.
5. **Docker Tag and Push** – Tags the image and pushes it to Docker Hub.

## Docker Image

The pipeline builds:

```bash
api-gateway:latest
```

It then tags and pushes the image to my dockerhub registry:

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

## About the Jenkinsfile

The credentials are injected into the pipeline using Jenkins' `withCredentials` block rather than being written directly into the Jenkinsfile.

- The pipeline is written using Jenkins Declarative Pipeline syntax and can be run from a Jenkins Pipeline job. 

- Docker Hub credentials are consumed through Jenkins Credentialsusing the credential ID: dockerhub-id; pull request/feature branch jobs stop before publication.

- Docker Hub credentials are consumed through Jenkins Credentials; pull request/feature branch jobs stop before publication.


## Next Steps
Some improvements I may be adding to the pipeline:

* Automated tests
* SonarQube code analysis
* Docker image vulnerability scanning
* Versioned Docker image tags
* GitHub webhook trigger
* Deployment to Kubernetes
* Argo CD for continuous deployment
