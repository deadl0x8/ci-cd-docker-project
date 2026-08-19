# CI/CD Docker Project

A simple Python Flask application containerized with Docker and integrated with a CI/CD pipeline using GitHub Actions.

The main purpose of this project is to understand how application code moves through a basic DevOps workflow — from development and testing to Docker image creation and publishing the image to Docker Hub.

## Architecture

![CI/CD Docker Project Architecture](architecture.png)

## Project Overview

This project contains a small Flask application with an automated CI/CD workflow.

The workflow is triggered when code is pushed to the `main` branch or when a Pull Request is created targeting `main`.

GitHub Actions automatically:

1. Checks out the repository.
2. Sets up Python.
3. Installs the application dependencies.
4. Runs the automated tests.
5. Builds the Docker image.
6. Logs in to Docker Hub using GitHub Secrets.
7. Pushes the Docker image to Docker Hub.

The published image can then be pulled and used to run the application as a Docker container.

---

## Technologies Used

- Python
- Flask
- Pytest
- Git
- GitHub
- GitHub Actions
- Docker
- Docker Compose
- Docker Hub

---

## Project Structure

```text
ci-cd-docker-project/
│
├── app/
│   ├── __init__.py
│   ├── app.py
│   └── requirements.txt
│
├── tests/
│   └── test_app.py
│
├── scripts/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── .dockerignore
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── architecture.png
└── README.md
```

## Application

The application is built using Flask.
It provides a basic application endpoint and a health-check endpoint.

### Application endpoint
```
GET /
```
Example:
```
CI/CD Docker Project is running!
```
### Health endpoint
```
GET /health
```
Example response:
{
  "status": "healthy"
}
The health endpoint is also used by Docker Compose to check whether the application is running correctly.

---

## Running Tests

The project uses Pytest for automated testing.
Run the tests locally with:
```text
python -m pytest
```
The tests verify that the application endpoints are working as expected.
The same tests are automatically executed by GitHub Actions whenever the CI pipeline runs.

---

## Docker

The application is packaged into a Docker image so that it can run consistently across different environments.

### Build the Docker image
```
docker build -t ci-cd-docker-project .
```
### Run the container
```
docker run -d -p 5000:5000 --name ci-cd-app ci-cd-docker-project
```
Check running containers:
```
docker ps
```
The application will be available at:
```
http://localhost:5000
```
To stop the container:
```
docker stop ci-cd-app
```
To remove the container:
```
docker rm ci-cd-app
```
---

## Docker Compose

The project also includes a docker-compose.yml file for running the application.
### Start the application
```
docker compose up -d --build
```
### Check the container
```
docker compose ps
```
The application container includes a health check that verifies the /health endpoint.
A healthy container should show:
```
healthy
```
### View logs
```
docker compose logs
```
For live logs:
```
docker compose logs -f
```
Press Ctrl + C to stop viewing the logs.
This does not stop the container.

### Stop the application
```
docker compose down
```
---

## CI/CD Pipeline

The CI/CD workflow is defined in:
```
.github/workflows/ci.yml
```
The workflow runs when:

Code is pushed to main
A Pull Request targets main

### Pipeline
```
Developer
    │
    │ git push / Pull Request
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Checkout code
    │
    ├── Set up Python
    │
    ├── Install dependencies
    │
    ├── Run Pytest
    │
    ├── Login to Docker Hub
    │
    ├── Build Docker image
    │
    └── Push Docker image
            │
            ▼
       Docker Hub
```
This allows the project to automatically test and package the application whenever changes are made.

---

## GitHub Actions

The workflow uses GitHub Actions to automate the CI/CD process.
The main stages are:

1. Checkout
GitHub Actions checks out the latest source code from the repository.

2. Python Setup
The workflow sets up Python 3.12.

3. Install Dependencies
The required Python packages are installed from:
```
app/requirements.txt
```

4. Run Tests
Pytest is executed using:
```
python -m pytest
```
The pipeline must pass the tests before continuing.

5. Build Docker Image
After the tests pass, GitHub Actions builds the Docker image using the project's Dockerfile.

6. Push to Docker Hub
The successfully built image is pushed to Docker Hub.

---

## Docker Hub

The Docker image is published to:
```
elnino08/ci-cd-docker-project:latest
```
The image can be pulled using:
```
docker pull elnino08/ci-cd-docker-project:latest
```
Then it can be run with:
```
docker run -d -p 5000:5000 --name ci-cd-app elnino08/ci-cd-docker-project:latest
```

---

## GitHub Secrets

Docker Hub authentication is handled using GitHub repository secrets.

The workflow uses:
```
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```
These credentials are not stored directly in the workflow file.
This keeps Docker Hub authentication information separate from the source code.

---


## Git Workflow

During development, I used a feature branch and Pull Request workflow instead of making all changes directly on main.

The workflow was:
```
feature/ci-pipeline
        │
        ▼
   Pull Request
        │
        ▼
 GitHub Actions
        │
        ├── Tests
        └── Docker Build
        │
        ▼
     Review
        │
        ▼
     main
```
This helped verify the CI pipeline before merging the changes into the main branch.

---

## Health Check

The application provides a health endpoint:
```
/health
```
Example:
```
{
  "status": "healthy"
}
```
Docker Compose uses this endpoint as its container health check.
This allows Docker to distinguish between a container that is simply running and an application that is actually responding correctly.

---

## Verification

The project was tested at different stages of the workflow.

### Local application
```
http://localhost:5000
```
### Health check
```
http://localhost:5000/health
```
Automated tests
```
python -m pytest
```
Docker image  
```
docker build -t ci-cd-docker-project .
```
Docker Hub image
```
docker pull elnino08/ci-cd-docker-project:latest
```
Docker container
```
docker run -d -p 5000:5000 --name ci-cd-app elnino08/ci-cd-docker-project:latest
```
The GitHub Actions pipeline successfully tested the application, built the Docker image, and pushed the image to Docker Hub.

---

## What I Learned

While building this project, I worked with:

Git branching and Pull Requests
GitHub repository management
GitHub Actions workflows
Automated testing with Pytest
Docker image creation
Docker containers
Docker Compose
Docker health checks
Docker Hub
GitHub Secrets
CI/CD concepts
Basic application deployment

The project helped me understand how different DevOps tools can be connected together instead of using each tool separately.

---

## Future Improvements

The current project focuses on the basic CI/CD workflow. Some improvements I would like to add are:

Docker image vulnerability scanning
Infrastructure provisioning using Terraform
Kubernetes deployment
Kubernetes readiness and liveness probes
Application monitoring
Prometheus and Grafana integration
Automated deployment to a cloud environment
Deployment rollback strategy

These improvements would extend the project from a basic CI/CD pipeline into a more complete DevOps deployment workflow.

---

## Conclusion

This project was built as a hands-on way to practice DevOps and CI/CD concepts.
It demonstrates a complete basic workflow where application code is tested automatically, packaged into a Docker image, and published to Docker Hub through GitHub Actions.
The project also uses Docker Compose and application health checks to verify that the containerized application is running correctly.

---

## Author

AYUSH MANKAR

---
