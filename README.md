# CI/CD Docker Project

A production-style DevOps project that demonstrates automated testing, Docker containerization, Docker Hub image publishing, Infrastructure as Code with Terraform, and automated deployment of a Flask application to an AWS EC2 instance using GitHub Actions.

The project demonstrates an end-to-end CI/CD workflow where application changes are automatically tested, containerized, published, and deployed to AWS.


## Architecture



```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Run Pytest
    │
    ├── Build Docker Image
    │
    ├── Push Image → Docker Hub
    │
    └── SSH Deployment
             │
             ▼
        AWS EC2
             │
       Docker Compose
             │
             ▼
        Flask App
             │
          Port 80
```

### AWS Infrastructure

The AWS infrastructure is provisioned and managed using Terraform.

```text
Terraform
   │
   ▼
AWS VPC
   │
   └── Public Subnet
          │
          ├── Internet Gateway
          │
          ├── Route Table
          │
          ├── Security Group
          │
          └── EC2 Instance
                 │
              Docker
                 │
             Flask App
```

Terraform manages the infrastructure configuration instead of requiring the AWS resources to be created manually.


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

* Python
* Flask
* Pytest
* Git
* GitHub
* GitHub Actions
* Docker
* Docker Compose
* Docker Hub
* AWS EC2
* AWS VPC
* AWS Security Groups
* Terraform
* SSH


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
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── terraform.tfvars
│   └── outputs.tf
│
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── deploy.yml
│
├── .dockerignore
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── architecture.png
└── README.md

 Terraform state files and generated `.terraform/` files are excluded from version control.

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


## Infrastructure as Code

AWS infrastructure is provisioned using Terraform.
The Terraform configuration creates and manages:

* VPC
* Public subnet
* Internet Gateway
* Public route table
* Security Group
* EC2 instance
* Elastic IP

Terraform variables are used for configurable values such as:

* AWS region
* VPC CIDR
* Subnet CIDR
* Availability Zone
* AMI ID
* EC2 instance type
* EC2 key pair
* SSH CIDR

### Terraform commands

Initialize Terraform:

```bash
terraform init
```

Format the configuration:

```bash
terraform fmt
```

Validate the configuration:

```bash
terraform validate
```

Review the infrastructure plan:

```bash
terraform plan
```

Apply the infrastructure:

```bash
terraform apply
```

The Terraform output provides the public IP address used for deployment.

---


## CI/CD Pipeline

The project uses GitHub Actions to automate both Continuous Integration and Continuous Deployment.

### Continuous Integration

When changes are pushed to the `main` branch:

1. GitHub Actions checks out the repository.
2. Python 3.12 is configured.
3. Application dependencies are installed.
4. Pytest executes the automated tests.
5. Docker Hub authentication is performed using GitHub Secrets.
6. The Docker image is built.
7. The image is pushed to Docker Hub.

### Continuous Deployment

After the CI process completes successfully:

1. GitHub Actions connects to the AWS EC2 instance using SSH.
2. The latest application code/image is deployed.
3. Docker Compose recreates the application container.
4. The Flask application becomes available through port 80.

```text
Git Push
   │
   ▼
GitHub Actions
   │
   ├── Pytest
   │
   ├── Docker Build
   │
   ├── Docker Hub Push
   │
   └── SSH
        │
        ▼
     AWS EC2
        │
        ▼
   Docker Compose
        │
        ▼
    Flask App
```
---

## AWS Deployment

The application is deployed to an AWS EC2 instance provisioned using Terraform.

The EC2 instance runs Docker and Docker Compose. GitHub Actions connects to the instance using an SSH key and performs the deployment automatically.

The application is exposed through HTTP port 80.

The AWS infrastructure consists of:

* VPC
* Public subnet
* Internet Gateway
* Route table
* Security Group
* EC2 instance
* Elastic IP

The Security Group allows HTTP traffic on port 80 and SSH traffic on port 22 for CI/CD deployment.

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


## GitHub Secrets

Docker Hub authentication is handled using GitHub repository secrets.

The workflow uses:
```
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
EC2_HOST
EC2_SSH_KEY
```
These credentials are not stored directly in the workflow file.
This keeps Docker Hub authentication information separate from the source code.

---


## Git Workflow

During development, I used a feature branch and Pull Request workflow instead of making all changes directly on `main`.

The workflow was:

```text
feature/ci-pipeline
        │
        ▼
   Pull Request
        │
        ▼
 GitHub Actions
        │
        ├── Run Tests
        └── Docker Build
        │
        ▼
      Review
        │
        ▼
      Merge
        │
        ▼
      main
        │
        ▼
 GitHub Actions
        │
        ├── Build & Push Docker Image
        │
        └── Deploy to AWS EC2
        │
        ▼
    Running Application
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

- Infrastructure as Code with Terraform
- AWS VPC networking
- EC2 provisioning
- AWS Security Groups
- SSH-based deployment
- CI/CD deployment automation
- Terraform variables and outputs

The project helped me understand how different DevOps tools can be connected together instead of using each tool separately.

---

## Future Improvements

The current project focuses on the basic CI/CD workflow. Some improvements I would like to add are:

- Docker image vulnerability scanning
- Kubernetes deployment
- Prometheus and Grafana monitoring
- Deployment rollback strategy
- Blue/green or rolling deployments
- Infrastructure remote state management


These improvements would extend the project from a basic CI/CD pipeline into a more complete DevOps deployment workflow.

---

## Conclusion

This project successfully demonstrates the design and implementation of an end-to-end CI/CD pipeline for a Dockerized Flask application. GitHub Actions automates testing, Docker image building, and deployment, while Docker provides consistent application packaging and execution.

The AWS infrastructure is provisioned and managed using Terraform, including the VPC, public subnet, Internet Gateway, route table, Security Group, and EC2 instance. The application is automatically deployed to the EC2 instance through SSH and Docker Compose.

Overall, the project provides practical experience with Cloud Computing, Infrastructure as Code, Containerization, CI/CD automation, AWS, Terraform, GitHub Actions, and Docker, demonstrating how modern DevOps practices can be used to automate application delivery from source code to a running cloud environment.
---

## Author

AYUSH MANKAR

---
