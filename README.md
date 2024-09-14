CI/CD Pipeline with Docker, GitHub Actions, and Minikube Kubernetes
This project demonstrates how to build a simple Node.js application, containerize it using Docker, automate the build and push process using GitHub Actions, and (optionally) deploy the application to a local Kubernetes cluster using Minikube.

Prerequisites
Before starting, ensure you have the following installed:

Docker
Node.js
Minikube (optional, for local Kubernetes deployment)
Kubectl (optional, for Kubernetes control)
Git

Project Setup
1. Node.js Application
We created a simple Node.js application. Here's an example of the server.js file:

const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

This sets up a basic server using Express that listens on port 3000.

2. Dockerfile
We containerized the Node.js application using Docker. Below is the Dockerfile used:

# Use Node.js base image
FROM node:16

# Set the working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose port 3000
EXPOSE 3000

# Run the application
CMD ["node", "server.js"]

# Use Node.js base image
FROM node:16

# Set the working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose port 3000
EXPOSE 3000

# Run the application
CMD ["node", "server.js"]

This file defines the build steps for our Node.js app, specifying the base image, installing dependencies, and copying the app's source code.

3. GitHub Actions Workflow
This is the CI/CD workflow in .github/workflows/ci-cd-pipeline.yml:

name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build Docker image
        run: docker build -t cicd-pipeline-demo:latest .

      - name: Log in to Docker Hub
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin

      - name: Push Docker image
        run: docker push ${{ secrets.DOCKER_USERNAME }}/cicd-pipeline-demo:latest

Next we deploy the deployment.yml and servivce.yml (Code is in repo)

This service exposes the Node.js app on port 30001, which allows access from outside the cluster.

To deploy to Kubernetes using Minikube, run:
kubectl apply -f ./k8s/deployment.yml
kubectl apply -f ./k8s/service.yml

Then, get the service URL by running:

minikube service node-app-service

Conclusion
This project walks you through setting up a basic CI/CD pipeline for a Node.js app using Docker and GitHub Actions, with the option to deploy to a local Kubernetes cluster via Minikube.

It covers everything from writing the application, containerizing it with Docker, setting up GitHub Actions to automate building and pushing the Docker image, and deploying it using Kubernetes (optional).

Feel free to explore and extend the project!
