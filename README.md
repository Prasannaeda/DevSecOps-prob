
# DevSecOps Project: Deploying a Node.js Full-Stack Application on EC2 using Jenkins CI/CD and Docker

## Project Description
This project involves deploying a Node.js full-stack application on an AWS EC2 instance using a Jenkins CI/CD pipeline. The application is containerized using Docker to ensure consistent and reliable deployments. Security practices are integrated into the CI/CD pipeline to align with DevSecOps principles.

---

## Table of Contents
- [Prerequisites](#prerequisites)
- [Architecture Overview](#architecture-overview)
- [Step-by-Step Implementation](#step-by-step-implementation)
- [Key Features](#key-features)
- [Conclusion](#conclusion)

---

## Prerequisites
1. **AWS Account:** Set up an AWS account and create an EC2 instance.
2. **Node.js Application:** A fully developed Node.js full-stack application.
3. **Jenkins Server:** Jenkins installed and configured on a server (can be your local machine or an EC2 instance).
4. **Docker Installed:** Docker installed on both the Jenkins server and the EC2 instance.
5. **AWS CLI Configured:** Set up AWS CLI on Jenkins for deployment automation.
6. **Git Repository:** Source code hosted in a version control system (e.g., GitHub).

---

## Architecture Overview
- **Development Environment:** Develop and test the Node.js application locally.
- **CI/CD Pipeline:** Build, test, and deploy the application using Jenkins.
- **Containerization:** Dockerize the application for consistent deployment.
- **Deployment:** Deploy the containerized application to an EC2 instance.

---

## Step-by-Step Implementation

### Step 1: Set Up EC2 Instance
1. Launch an EC2 instance in AWS.
2. Install Docker on the EC2 instance:
   ```bash
   sudo apt-get update
   sudo apt-get install -y docker.io
   sudo systemctl start docker
   sudo systemctl enable docker
   ```
3. Configure the instance with a security group allowing SSH, HTTP, and other necessary ports.

### Step 2: Dockerize the Node.js Application
1. Create a `Dockerfile` in your application root directory:
   ```dockerfile
   FROM node:14
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   EXPOSE 3000
   CMD ["npm", "start"]
   ```
2. Build the Docker image locally and test it:
   ```bash
   docker build -t nodejs-app .
   docker run -p 3000:3000 nodejs-app
   ```

### Step 3: Configure Jenkins Pipeline
1. Install the necessary plugins in Jenkins:
   - Docker Pipeline
   - Git
   - Pipeline
2. Create a Jenkins Pipeline job.
3. Define the pipeline script in Jenkins:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Clone Repository') {
               steps {
                   git 'https://github.com/your-repo/nodejs-app.git'
               }
           }
           stage('Build Docker Image') {
               steps {
                   sh 'docker build -t nodejs-app .'
               }
           }
           stage('Push to Docker Hub') {
               steps {
                   sh '''
                   docker login -u $DOCKER_USER -p $DOCKER_PASSWORD
                   docker tag nodejs-app $DOCKER_USER/nodejs-app
                   docker push $DOCKER_USER/nodejs-app
                   '''
               }
           }
           stage('Deploy to EC2') {
               steps {
                   sshagent(['ec2-ssh-key']) {
                       sh '''
                       ssh -o StrictHostKeyChecking=no ubuntu@<EC2_PUBLIC_IP> "docker pull $DOCKER_USER/nodejs-app && docker run -d -p 80:3000 $DOCKER_USER/nodejs-app"
                       '''
                   }
               }
           }
       }
   }
   ```
4. Replace `<EC2_PUBLIC_IP>` with your EC2 instance's public IP and set up Jenkins credentials for SSH and Docker Hub.

### Step 4: Test the Deployment
1. Access the application using the EC2 instance's public IP or domain.
2. Verify that the application is running correctly.

---

## Key Features
- **Automated CI/CD Pipeline:** Ensures continuous integration and deployment.
- **Containerization:** Docker ensures consistent application behavior across environments.
- **Security Integration:** Adherence to DevSecOps principles with secure practices.
- **Scalable Deployment:** Easily deployable on multiple EC2 instances.

---

## Conclusion
This project demonstrates a secure and automated approach to deploying a Node.js full-stack application using Jenkins CI/CD and Docker. It showcases the integration of development, operations, and security practices in line with DevSecOps principles.
