DevSecOps Project: Deploying a Node.js Full-Stack Application on EC2 using Jenkins CI/CD and Docker
Project Description
This project involves deploying a Node.js full-stack application on an AWS EC2 instance using a Jenkins CI/CD pipeline. The application is containerized using Docker to ensure consistent and reliable deployments. Security practices are integrated into the CI/CD pipeline to align with DevSecOps principles.

Table of Contents
Prerequisites
Architecture Overview
Step-by-Step Implementation
Key Features
Conclusion
Prerequisites
AWS Account: Set up an AWS account and create an EC2 instance.
Node.js Application: A fully developed Node.js full-stack application.
Jenkins Server: Jenkins installed and configured on a server (can be your local machine or an EC2 instance).
Docker Installed: Docker installed on both the Jenkins server and the EC2 instance.
AWS CLI Configured: Set up AWS CLI on Jenkins for deployment automation.
Git Repository: Source code hosted in a version control system (e.g., GitHub).
Architecture Overview
Development Environment: Develop and test the Node.js application locally.
CI/CD Pipeline: Build, test, and deploy the application using Jenkins.
Containerization: Dockerize the application for consistent deployment.
Deployment: Deploy the containerized application to an EC2 instance.
Step-by-Step Implementation
Step 1: Set Up EC2 Instance
Launch an EC2 instance in AWS.
Install Docker on the EC2 instance:
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
Configure the instance with a security group allowing SSH, HTTP, and other necessary ports.
Step 2: Dockerize the Node.js Application
Create a Dockerfile in your application root directory:
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
Build the Docker image locally and test it:
docker build -t nodejs-app .
docker run -p 3000:3000 nodejs-app
Step 3: Configure Jenkins Pipeline
Install the necessary plugins in Jenkins:
Docker Pipeline
Git
Pipeline
Create a Jenkins Pipeline job.
Define the pipeline script in Jenkins:
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
Replace <EC2_PUBLIC_IP> with your EC2 instance's public IP and set up Jenkins credentials for SSH and Docker Hub.
Step 4: Test the Deployment
Access the application using the EC2 instance's public IP or domain.
Verify that the application is running correctly.
Key Features
Automated CI/CD Pipeline: Ensures continuous integration and deployment.
Containerization: Docker ensures consistent application behavior across environments.
Security Integration: Adherence to DevSecOps principles with secure practices.
Scalable Deployment: Easily deployable on multiple EC2 instances.
Conclusion
This project demonstrates a secure and automated approach to deploying a Node.js full-stack application using Jenkins CI/CD and Docker. It showcases the integration of development, operations, and security practices in line with DevSecOps principles.


On Mon, Dec 30, 2024 at 9:03 PM Eda Prasanna <edaprasanna1@gmail.com> wrote:
Prasannaeda
