# Jenkins CI/CD Pipeline

A Jenkins Declarative Pipeline that automates the CI/CD workflow for the `api-gateway` service.

## Technologies

`Jenkins` · `Node.js` · `npm` · `Docker` · `Docker Hub` · `Git`

## Pipeline

The pipeline:

- Clones the GitHub repository
- Installs Node.js dependencies
- Builds the Docker image
- Authenticates with Docker Hub using Jenkins credentials
- Tags and pushes the image to Docker Hub

## Jenkins Requirements

- Jenkins with NodeJS `23.0.0` configured
- Docker installed on the Jenkins agent
- Git installed
- Docker Hub credentials configured in Jenkins with ID:
  `dockerhub-id`

## Project Structure

```text
jenkins-cicd-pipelines/
└── Jenkinsfile
````

## What This Demonstrates

* Jenkins Declarative Pipelines
* CI/CD automation
* Docker image builds
* Docker Hub integration
* Jenkins credential management
* Git and Node.js integration

```

## Future Improvements

- Add code quality checks
- Add Trivy Docker image vulnerability scanning
- Use versioned Docker image tags
- Add automated tests
- Add Kubernetes deployment

Adding Trivy before Docker Push, so a vulnerable image can fail the pipeline before it reaches my image registry

