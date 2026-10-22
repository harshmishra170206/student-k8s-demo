# Kubernetes CI/CD Beginner Lab - Command Reference

This guide contains all the terminal commands needed to complete the CI/CD pipeline lab, organized by stage.

## Stage 2: Prerequisites and Installation Checks

Verify that your local environment is set up correctly before starting:

```bash
git --version
python --version
docker --version
docker info
kubectl version --client
kubectl get nodes
```

## Stage 3: Run Locally (Virtual Environment & Tests)

Set up your Python environment, install dependencies, and run tests locally.

```bash
# Create the virtual environment
python -m venv venv

# Activate the virtual environment (Windows PowerShell)
.\.venv\Scripts\Activate.ps1

# OR if blocked by execution policy, bypass it for this session:
Set-ExecutionPolicy -Scope Process ExecutionPolicy Bypass

# Install dependencies, run tests, and start the app
python -m pip install -r requirements.txt
python -m pytest -v
python app.py
```

## Stage 4: Build and Run the Docker Image

Package your application into a Docker container and test it locally.

```bash
# Build the image (don't forget the dot at the end!)
docker build -t student-k8s-demo:v1 .

# List local images
docker images

# Run the container locally for testing
docker run --rm -d --name student-demo -p 5000:5000 student-k8s-demo:v1

# Check running containers and view logs
docker ps
docker logs student-demo

# Stop the container
docker stop student-demo
```

## Stage 5: Push Source Code to GitHub

Initialize your repository and push the initial setup to GitHub.

```bash
# Initialize Git and make the first commit
git init
git add .
git commit -m "Initial Flask application and Docker setup"

# Rename branch to main and push to GitHub (Replace YOUR_USERNAME)
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/student-k8s-demo.git
git push -u origin main
```

## Stage 6 & 7: Update CI/CD Pipeline (GitHub Actions)

Commit and push your GitHub Actions workflow files to automate testing and building.

```bash
# Push the initial pipeline
git add .github/workflows/pipeline.yml
git commit -m "Add automated CI tests"
git push

# Push the updated pipeline with the Docker build stage
git add .github/workflows/pipeline.yml
git commit -m "Add Docker build and publish stage"
git push
```

## Stage 8: Deploy the Image to Kubernetes

Apply your manifests to deploy the container to your local Docker Desktop Kubernetes cluster.

```bash
# Verify Kubernetes cluster connection
kubectl config current-context
kubectl get nodes

# Apply the Deployment and Service manifests
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

# Check the deployment status and view resources
kubectl rollout status deployment/student-k8s-demo
kubectl get deployments
kubectl get pods
kubectl get services
```

## Stage 9: Demonstrate an Application Update

Push a code change and manually update the Kubernetes deployment with the new image.

```bash
# Commit the code changes
git add .
git commit -m "Update application message"
git push

# Update the running Kubernetes deployment (Replace placeholders with actual values!)
kubectl set image deployment/student-k8s-demo student-k8s-demo=YOUR_DOCKERHUB_USERNAME/student-k8s-demo:NEW_COMMIT_SHA

# Monitor the update
kubectl rollout status deployment/student-k8s-demo
kubectl get pods
```

## Stage 11: Troubleshooting Commands

Use these commands if your pods are crashing, failing to pull images, or routing incorrectly.

```bash
kubectl get all
kubectl describe deployment student-k8s-demo
kubectl logs deployment/student-k8s-demo
kubectl get events --sort-by=.metadata.creationTimestamp
```

## EXTRA: Local Browser Network Bypass (Port-Forwarding)

If Docker Desktop networking fails on Windows (meaning your browser cannot reach `localhost:30080`), use this command to tunnel directly to the service. Keep the terminal window open, then visit `http://127.0.0.1:30080/` in your browser.

```bash
# For the standard lab naming convention:
kubectl port-forward service/student-k8s-demo 30080:80

# For a custom project name (e.g., my-api-project):
kubectl port-forward service/my-api-project 30080:80
```
