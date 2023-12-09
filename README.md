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
    * app.py (file containing code for a Flask application)
### 2.  Configure AWS Credentials in GitHub Secrets
* Create an IAM user and the attach the necessary permissions to push the images in ECR repo:
    * AmazonEC2ContainerRegistryFullAccess
* Within the IAM User settings, generate an access key and secret key.
* Go to your GitHub repository > Settings > Security > Secrets and Variables > Actions > New Repository Secret
* Add two secrets: AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY containing your AWS IAM user credentials with ECR permissions in New Repository Secret
### 3. Create an AWS ECR repository
* Go to the AWS Management Console and navigate to the Amazon ECR service.
* Click "Create repository".
* Name your repository and configure repository settings if necessary.
* Enable "Scan on push" to perform automatic vulnerability scanning on pushed Docker images.
### 4. Create GitHub Actions Workflow YAML File
* Create a ".github/workflows" directory if it doesn't exist in your GitHub repository, and add a YAML file, e.g., "deploy.yml", paste the following YAML code into the "deploy.yml" file:
~~~
name: Deploy to ECR

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
jobs:
  
  build:
    
    name: Build Image
    runs-on: ubuntu-latest
    steps:

    - name: Check out code
      uses: actions/checkout@v2
    
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: <your-aws-region>

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build, tag, and push image to Amazon ECR
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: <your-ecr-repo>
        IMAGE_TAG: <your-image-tag>
      run: |
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
~~~
* Replace your-ecr-repo, your-aws-region, your-image-tag with your actual AWS ECR details and Docker image name.
* Save the changes to the "deploy.yml" file.
* Commit and push this file to your GitHub repository.
### 5. Testing the Workflow:
* Make a code change in your repository and push it to the main branch.
* Go to the "Actions" tab in your GitHub repository to monitor the workflow progress.
* Check your AWS ECR repository for the newly pushed Docker image.
### 6. Add Email Notification Step to the GitHub Actions Workflow
* According to your requirements, you can configure your workflow to send email notifications based on the status of workflow.
* Add the following step at the end of your GitHub Actions workflow YAML file (deploy.yml) to send email notifications:
~~~
- name: Send mail
      if: always()
      uses: dawidd6/action-send-mail@v2
      with:
          # mail server settings
          server_address: smtp.gmail.com
          server_port: 465
          # user credentials
          username: ${{ secrets.EMAIL_USERNAME }}
          password: ${{ secrets.EMAIL_PASSWORD }}
          # email subject
          subject: ${{ github.job }} job of ${{ github.repository }} has ${{ job.status }}
          # email body as text
          body: ${{ github.job }} job in worflow ${{ github.workflow }} of ${{ github.repository }} has ${{ job.status }}
          # comma-separated string, send email to
          to: <username@email.com>
          # from email name
          from: <username>
~~~
* The above configuration will send notifications to any specified email based on the job status.
# Conclusion
This documentation outlines the steps to automate Docker image builds and uploads to AWS ECR using GitHub Actions. Customize the workflow YAML file and secrets according to your specific project requirements.
