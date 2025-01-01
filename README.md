
# Node.js Application and Its Helm Chart

This repository contains a Node.js application along with its Helm chart for easy deployment on Kubernetes clusters.

## Application Overview

The Node.js application in this repository is a basic web server that serves a static message. It's designed to demonstrate the structure of a Node.js project and its deployment using Helm.

### Features

- Simple Node.js web server.
- Dockerfile for containerization.
- Helm chart for Kubernetes deployments.

## Prerequisites

- Node.js
- Docker
- Kubernetes cluster
- Helm 3

## Getting Started

### Local Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/Abhin-Anilkumar/Nodejs-Application-and-its-helm-Chart.git
   cd Nodejs-Application-and-its-helm-Chart

2. Install dependencies:
   ```bash 
    npm install
3. Run the application:
   ```bash 
   node app.js

### Containerization

1. Build the Docker image:
   
   ```bash 
   docker build -t nodejs-app .
2. Run the Docker container:

   ```bash 
   docker run -p 8080:8080 nodejs-app
Deploying with Helm
Ensure your Kubernetes cluster is up and running.

### Install the Helm chart:

1.  Run the below commad 
    ```bash
    cd nodejs-application
    helm install nodejs-app . -n <namespace>

2. Verify the deployment:

   ```bash 
   kubectl get all

### Configuration

The Helm chart is configurable. Modify values.yaml to change the default configuration like the number of replicas, resource limits, and more.

