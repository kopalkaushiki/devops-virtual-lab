VLE 5 
GitHub stores the code → GitHub Actions builds the Docker image → Docker Hub stores it → Kubernetes deploys and scales it → Terraform provisions infrastructure → Ansible automates configuration.
final structure :

Git/GitHub
     ↓
GitHub Actions
     ↓
Docker
     ↓
Docker Hub
     ↓
Kubernetes / Minikube
     ↓
Running Application

Terraform → Infrastructure provisioning
Ansible   → Configuration automation

1. app/index.html:
This is the application that we're deploying.
index.html
    ↓
Docker
    ↓
Kubernetes
    ↓
Browser


2. app/Dockerfile:
What each line does
FROM nginx:latest
Uses NGINX as the base image.
COPY index.html /usr/share/nginx/html/index.html
Copies your HTML file into NGINX's default web-server directory.
  So:
  Your app
   ↓
  Dockerfile
   ↓
  NGINX container
   ↓
  Web application

When you ran:
docker build -t devops-lab-app ./app
Docker created the image.

3. k8s/deployment.yml

This tells Kubernetes how to run your application.
Important parts:
kind: Deployment //Creates a Kubernetes Deployment.
replicas: 2 //This is your scaling.

Kubernetes maintains:
  Deployment
   │
   ├── Pod 1
   │    └── NGINX
   │
   └── Pod 2
        └── NGINX

If one pod crashes, Kubernetes can create another one.

Image
image: YOUR_DOCKERHUB_USERNAME/devops-lab-app:latest

Kubernetes pulls the image from Docker Hub.


4. k8s/service.yml
Pods are not normally accessed directly from outside the cluster.
The Service provides a stable way to access them.


type: NodePort //This exposes the application outside the Kubernetes cluster.
selector:
  app: devops //connects the Service to the pods having: labels: app: devops
Browser
   ↓
NodePort Service
   ↓
 ┌─────────┐
 │         │
Pod 1    Pod 2


5. .github/workflows/cicd.yml
This is the CI/CD automation.

Whenever you do: git push origin main, GitHub Actions automatically runs.

git push ->GitHub Actions ->Checkout code ->Login to Docker Hub ->docker build->docker push-> Docker Hub
That's your Continuous Integration / image delivery part.

6. terraform/main.tf
This demonstrates Infrastructure as Code.
What Terraform does ,Instead of manually doing: docker pull nginx ,docker run ...
Terraform defines the desired infrastructure as code.
main.tf ->terraform apply->NGINX Docker container->localhost:8081

 
7. ansible/setup.yml

This demonstrates Configuration Management.
Ansible executes these tasks automatically.

You ran:
ansible-playbook setup.yml
and it verified:
Docker version 29.1.3
Kubernetes Client Version: v1.37.0
