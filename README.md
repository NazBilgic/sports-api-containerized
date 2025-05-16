# Sports API Management System

This project demonstrates how to build a containerized API management system for querying sports data. It uses Amazon ECS (Fargate) to run a Flask application in containers, Amazon API Gateway to expose REST endpoints, and an external Sports API for real-time data. The project showcases API management, container orchestration, and secure AWS integrations.

## Description

The system exposes a REST API endpoint for real-time sports data. A Dockerized Flask backend is deployed to ECS using Fargate, and the API is accessible via Application Load Balancer and API Gateway.

## Features

- REST API for real-time sports data
- Flask app containerized with Docker
- Deployed using ECS with Fargate
- API Gateway integration for public access
- Scalable, serverless, and secure architecture

## Technologies Used

- AWS ECS (Fargate)
- Amazon API Gateway
- Docker
- Python (Flask)
- IAM (custom roles and policies)
- CloudWatch for monitoring

## Project Structure

sports-api-management/
├── app.py
├── Dockerfile
├── requirements.txt
├── .gitignore
└── README.md

## Prerequisites

- AWS Account
- Sports API Key (from serpapi.com)
- Docker installed locally
- AWS CLI installed and configured
- Python 3.x with Serpapi package installed (`pip install google-search-results`)

## Setup Instructions

### 1. Clone the repository

git clone https://github.com/NazBilgic/sports-api-containerized.git  
cd sports-api-containerized

### 2. Create an Amazon ECR Repository

aws ecr create-repository --repository-name sports-api --region us-east-1

### 3. Build and Push Docker Image

Authenticate to ECR:

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

Build the Docker image:

docker build --platform linux/amd64 -t sports-api .

Tag the image:

docker tag sports-api:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest

Push the image:

docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest

## Deploy with ECS and Fargate

### 1. Create an ECS Cluster

- Go to ECS Console → Create Cluster
- Select Fargate
- Name: sports-api-cluster

### 2. Create Task Definition

- Task family: sports-api-task
- Launch type: Fargate
- Container:
  - Name: sports-api-container
  - Image: sports-api:sports-api-latest
  - Port: 8080
- Add environment variable:
  - Key: SPORTS_API_KEY
  - Value: your SerpAPI key

### 3. Create Service

- Go to ECS Cluster → Create Service
- Launch type: Fargate
- Task definition: sports-api-task
- Desired count: 2
- Create new security group: allow all TCP (for testing)
- Create new Application Load Balancer
- Health check path: /sports

### 4. Test ALB

Visit:  
http://<alb-dns-name>/sports

## Configure API Gateway

### 1. Create REST API

- Go to API Gateway → Create REST API
- Resource: /sports
- Method: GET
- Integration type: HTTP Proxy
- Endpoint: http://<alb-dns-name>/sports

### 2. Deploy

- Create a deployment stage (e.g., prod)
- Note the public endpoint URL

Test it:  
curl https://<api-id>.execute-api.us-east-1.amazonaws.com/prod/sports

## What You Learned

- How to containerize and deploy a Flask app on ECS
- Using Fargate for serverless container orchestration
- Creating a public API via API Gateway
- Secure integration of external APIs into AWS-based systems

## Future Enhancements

- Add caching with ElastiCache
- Store logs or preferences in DynamoDB
- Protect API Gateway with keys or IAM auth
- Implement CI/CD pipeline for automated builds


Naz Bilgiç  
linkedin.com/in/nazbilgic
