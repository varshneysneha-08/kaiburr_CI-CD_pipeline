# Kaiburr Task 4 : CI/CD Pipeline

This README gives a complete detail on the *CI/CD pipeline* setup using *GitHub Actions* for the frontend application of the Task. The pipeline includes:

* Code checkout
* Code build
* Docker image build
* Docker image push to Docker Hub

---

## ⚙ CI/CD Pipeline Overview

The GitHub Actions workflow performs the following steps:

1. *Trigger on Push or PR to main*: The workflow is triggered when code is pushed to or a pull request is made to the main branch.
2. *Checkout Code*: Uses the actions/checkout@v3 action to pull the repository.
3. *Set Up Environment*: Sets up Node.js environment.
4. *Install Dependencies & Build Code*: Installs required packages and builds the project.
5. *Docker Build*: Builds a Docker image using the Dockerfile.
6. *Docker Push (Optional)*: Pushes the image to Docker Hub (or another registry).

---

## 🛠 GitHub Actions Workflow Configuration

Below is the deploy.yml file placed in .github/workflows/deploy.yml. The file triggers on a push on the main branch and uses a docker image to push and build.

yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-and-dockerize:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

      - name: Build Vite Project
        run: npm run build

      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build Docker Image
        run: docker build -t ${{ secrets.DOCKER_USERNAME }}/react-vite-app .

      - name: Push Docker Image
        run: docker push ${{ secrets.DOCKER_USERNAME }}/react-vite-app


## 🔐 Setting Up Secrets

Before running the pipeline, store the following secrets in your repository:

1. DOCKER_USERNAME – Your Docker Hub username
2. DOCKER_PASSWORD – Your Docker Hub password or access token

Go to *Settings → Secrets → Actions → New Repository Secret*.

---

## 🐳 Dockerfile

The repository contains the following Dockerfile at the root:

dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

RUN npm run build

EXPOSE 5173

CMD ["npm", "run", "preview"]

---

## 📦 Output
![image](https://github.com/user-attachments/assets/5c65901f-5e8a-45d0-8b88-ec4ea45316f5)

