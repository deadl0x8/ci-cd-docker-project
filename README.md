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

on health checks to verify that the containerized application is running correctly.
