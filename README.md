# Flask and Express Application Deployment Using Kubernetes (Minikube)

## 1. Project Overview

This project demonstrates the deployment of a containerized web application using **Kubernetes and Minikube**.

The application consists of two components:

- **Frontend:** A Node.js application built using Express.js.
- **Backend:** A Python application built using Flask.

Both components are containerized using Docker and deployed as separate Kubernetes Deployments. Kubernetes Services allow the frontend to communicate with the backend internally.

The project was implemented and tested locally using Minikube running with the Docker driver.

## 2. Project Architecture

The application follows a frontend-backend architecture:

```text
                 User
                   |
                   v
          Frontend (Express.js)
              Port 3000
                   |
                   | Kubernetes Service
                   | http://backend:5000
                   v
           Backend (Flask)
              Port 5000
                   |
                   v
          Application API
```

### Frontend
- Developed using Node.js and Express.js.
- Runs on port `3000`.
- Provides the user interface for submitting and viewing messages.
- Communicates with the Flask backend through the Kubernetes Service named `backend`.

### Backend
- Developed using Python and Flask.
- Runs on port `5000`.
- Provides REST API endpoints to receive and retrieve messages.
- Handles requests from the frontend.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| Python | Backend programming language |
| Flask | Backend web framework |
| Node.js | Frontend runtime |
| Express.js | Frontend web framework |
| Docker | Containerization |
| Kubernetes | Container orchestration |
| Minikube | Local Kubernetes cluster |
| kubectl | Kubernetes command-line management |
| Ubuntu WSL2 | Development environment |
| Docker Desktop | Docker engine |

## 4. Repository Structure

```text
flask-express-kubernetes-minikube/
│
├── Backend/
│   ├── app.py
│   ├── dockerfile
│   └── requirements.txt
│
├── Frontend/
│   ├── app.js
│   ├── dockerfile
│   └── package.json
│
├── Kubernetes/
│   ├── backend.yaml
│   └── frontend.yaml
│
├── screenshots/
│   └── Deployment and testing screenshots
│
└── README.md
```

## 5. Application Containerization

Separate Docker images were created for the frontend and backend.

### Build the Backend Image

```bash
docker build -t flask-backend:1.0 -f Backend/dockerfile Backend
```

### Build the Frontend Image

```bash
docker build -t express-frontend:1.0 -f Frontend/dockerfile Frontend
```

### Verify Docker Images

```bash
docker images
```

The images used for deployment are:

- `flask-backend:1.0`
- `express-frontend:1.0`

## 6. Minikube Cluster Setup

Minikube was used to create a local Kubernetes cluster.

Start the cluster using the Docker driver:

```bash
minikube start --driver=docker
```

Verify the cluster:

```bash
minikube status
kubectl get nodes
```

The Minikube node should show a `Ready` status.

## 7. Load Docker Images into Minikube

The locally built Docker images were loaded into Minikube so Kubernetes could use them.

```bash
minikube image load flask-backend:1.0
minikube image load express-frontend:1.0
```

The Kubernetes manifests use the local images with `imagePullPolicy: Never`.

## 8. Kubernetes Deployment

Kubernetes YAML manifests define the Deployments and Services for both application components.

### Deploy the Backend

```bash
kubectl apply -f Kubernetes/backend.yaml
```

The backend Deployment runs the Flask application, while its ClusterIP Service makes it accessible internally within the Kubernetes cluster.

### Deploy the Frontend

```bash
kubectl apply -f Kubernetes/frontend.yaml
```

The frontend Deployment runs the Express application.

The frontend is configured to communicate with the backend using:

```text
BACKEND_URL=http://backend:5000
```

Here, `backend` is the Kubernetes Service name. Kubernetes DNS resolves this name to the backend Service within the cluster.

## 9. Verify Kubernetes Resources

Use the following commands to verify the deployment:

### Check Nodes

```bash
kubectl get nodes
```

### Check Deployments

```bash
kubectl get deployments
```

### Check Pods

```bash
kubectl get pods
```

### Check Services

```bash
kubectl get services
```

The frontend and backend Pods should reach the `Running` state, and their Deployments should become available.

## 10. Access and Test the Application

The frontend was accessed locally using Kubernetes port forwarding.

Run:

```bash
kubectl port-forward service/frontend 3000:3000
```

Open the following URL in a browser:

```text
http://localhost:3000
```

The application allows users to submit messages through the frontend and view the submitted messages.

The backend can also be accessed locally in a separate terminal using:

```bash
kubectl port-forward service/backend 5000:5000
```

Backend endpoint:

```text
http://localhost:5000/
```

API endpoint:

```text
http://localhost:5000/api/data
```

## 11. Screenshots and Documentation

The `screenshots/` directory contains visual evidence of the project setup, Docker image creation, Minikube cluster, Kubernetes deployments, running Pods and Services, and application testing.

These screenshots document the implementation and verification of the Kubernetes deployment.

## 12. Application Limitation

The backend currently stores messages in an in-memory Python list. This is suitable for demonstrating application functionality, but the messages are not persistent.

If the backend process or Pod restarts, the stored messages may be lost. A database and persistent storage would be required for production use.

## 13. Conclusion

This project demonstrates how a multi-component web application can be containerized using Docker and deployed locally using Kubernetes with Minikube.

The frontend and backend run as separate Kubernetes workloads and communicate through an internal Kubernetes Service. The application was accessed and tested locally using port forwarding.

The project provides practical experience with Docker containerization, Kubernetes Deployments, Services, YAML manifests, Minikube and application testing.

## 14. Repository

GitHub Repository: (https://github.com/pratikdeshmukh45/flask-express-kubernetes-minikube.git)