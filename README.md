# GitHub Actions Workflow: Automating Docker Image Builds and Uploads to AWS ECR
Step-by-Step Guide for how to set up a GitHub Actions workflow to automate the build process of Docker images and push them to AWS Elastic Container Registry (ECR) whenever code changes are made or pull requests are initiated.

## Pre-Requisites
* GitHub Repository: You have admin access to the repository where you want to set up the workflow.
* AWS Account: Access to AWS services with permissions to manage ECR.
* Dockerfile: The repository contains a Dockerfile to build the Docker image.

## Steps
### 1. Create a GitHub Repository and add Necessary Files
* Login to Github and create a private repository
* Clone the newly created repository to your local machine.
* Inside the cloned repository directory, add the required files and push to the repository:
    * Dockerfile (To build the required docker image)
    * app.py (file containing code for a simple Flask application)
### 2. 
