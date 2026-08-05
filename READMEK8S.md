# Microservices Deployment on Kubernetes using Minikube

## Project Overview

This project demonstrates the deployment of a containerized microservices application on Kubernetes using Minikube.

The application consists of four Node.js microservices:

| Service         | Port |
| --------------- | ---- |
| User Service    | 3000 |
| Product Service | 3001 |
| Order Service   | 3002 |
| Gateway Service | 3003 |

Each service is deployed using Kubernetes Deployments and exposed internally using Kubernetes ClusterIP Services.

---

# Architecture

```
                    +----------------+
                    | Gateway Service|
                    |      :3003     |
                    +--------+-------+
                             |
        -----------------------------------------
        |                  |                    |
        |                  |                    |
+---------------+ +----------------+ +----------------+
| User Service  | | Product Service | | Order Service |
|     :3000     | |      :3001      | |     :3002     |
+---------------+ +----------------+ +----------------+
```

Communication between services is handled using Kubernetes internal DNS service discovery.

Example:

```
http://user-service:3000
http://product-service:3001
http://order-service:3002
```

---

# Prerequisites

Install the following tools:

* Docker Desktop
* Kubernetes kubectl
* Minikube

Verify installations:

```bash
docker --version

kubectl version

minikube version
```

---

# Minikube Setup

Start Minikube:

```bash
minikube start --driver=docker
```

Verify cluster status:

```bash
minikube status
```

Check Kubernetes node:

```bash
kubectl get nodes
```

Expected output:

```
NAME       STATUS
minikube   Ready
```

---

# Docker Images

The following Docker images are used:

```
microservices-user-service:latest

microservices-product-service:latest

microservices-order-service:latest

microservices-gateway-service:latest
```

Verify images:

```bash
docker images
```

Load images into Minikube:

```bash
minikube image load microservices-user-service:latest

minikube image load microservices-product-service:latest

minikube image load microservices-order-service:latest

minikube image load microservices-gateway-service:latest
```

Verify images inside Minikube:

```bash
minikube image ls
```

---

# Kubernetes Deployment

## Deploy Microservices

Apply Deployment manifests:

```bash
kubectl apply -f deployments/
```

Apply Service manifests:

```bash
kubectl apply -f services/
```

---

# Verify Deployments

Check deployments:

```bash
kubectl get deployments
```

Expected:

```
NAME                 READY
user-service         1/1
product-service      1/1
order-service        1/1
gateway-service      1/1
```

---

# Verify Pods

Check running pods:

```bash
kubectl get pods
```

Expected:

```
NAME                         STATUS

user-service                 Running

product-service              Running

order-service                Running

gateway-service              Running
```

---

# Verify Services

Check Kubernetes Services:

```bash
kubectl get services
```

Expected:

```
NAME                 TYPE

user-service         ClusterIP

product-service      ClusterIP

order-service        ClusterIP

gateway-service      ClusterIP
```

---

# Service Communication Testing

Enter the Gateway Pod:

```bash
kubectl exec -it <gateway-pod-name> -- sh
```

Test communication:

```bash
curl http://user-service:3000

curl http://product-service:3001

curl http://order-service:3002
```

Successful responses confirm internal Kubernetes service discovery.

---

# Access Gateway Service

Since services use ClusterIP, access the gateway using port forwarding:

```bash
kubectl port-forward service/gateway-service 3003:3003
```

Open:

```
http://localhost:3003
```

or test:

```bash
curl http://localhost:3003
```

---

# Viewing Logs

View Gateway logs:

```bash
kubectl logs deployment/gateway-service
```

View individual pod logs:

```bash
kubectl logs <pod-name>
```

---

# Troubleshooting

## Check Pod Errors

```bash
kubectl describe pod <pod-name>
```

---

## Check Container Logs

```bash
kubectl logs <pod-name>
```

---

## ImagePullBackOff Error

Verify image exists:

```bash
docker images
```

Load image into Minikube:

```bash
minikube image load <image-name>:latest
```

Ensure Deployment contains:

```yaml
imagePullPolicy: Never
```

when using local images.

---

## Pod Restarting / CrashLoopBackOff

Check logs:

```bash
kubectl logs <pod-name>
```

Check events:

```bash
kubectl describe pod <pod-name>
```

---

# Project Structure

```

├── deployments/
│   ├── user-service.yaml
│   ├── product-service.yaml
│   ├── order-service.yaml
│   └── gateway-service.yaml
│
├── services/
│   ├── user-service.yaml
│   ├── product-service.yaml
│   ├── order-service.yaml
│   └── gateway-service.yaml
│
├── screenshots/
│   ├── pods.png
│   ├── logs.png
│   └── service-test.png
│
└── README.md
```

---

# Screenshots Included

The following screenshots are included as proof of deployment:

1. Running Kubernetes pods

Command:

```bash
kubectl get pods
```

Screenshot:

```
screenshots/pods.png
```

---

2. Application logs

Command:

```bash
kubectl logs deployment/gateway-service
```

Screenshot:

```
screenshots/logs.png
```

---

3. Service testing

Command:

```bash
kubectl port-forward service/gateway-service 3003:3003
```

Screenshot:

```
screenshots/service-test.png
```

---

# Challenges Faced and Solutions

During the Kubernetes deployment process, the following challenges were encountered and resolved:

---

## 1. Docker Image Pull Errors (`ImagePullBackOff` / `ErrImagePull`)

### Issue

Kubernetes was unable to start pods because it tried to pull images from Docker Hub that were not available.

Example error:

```
Failed to pull image "uniquechanz/user-service:latest"
ImagePullBackOff
```

### Root Cause

The Kubernetes Deployment YAML contained incorrect image references. The images existed locally with different names:

```
microservices-user-service:latest
microservices-product-service:latest
microservices-order-service:latest
microservices-gateway-service:latest
```

but Kubernetes was trying to pull:

```
uniquechanz/user-service:latest
```

### Solution

Updated the Deployment manifests with the correct local image names:

```yaml
image: microservices-user-service:latest
imagePullPolicy: Never
```

Loaded Docker images into Minikube:

```bash
minikube image load <image-name>:latest
```

---

## 2. Docker Compose Build Context Error

### Issue

Docker Compose failed while building services.

Error:

```
unable to prepare context:
path "./user-service" not found
```

### Root Cause

The paths specified in `docker-compose.yml` did not match the actual project folder structure.

### Solution

Updated the `build.context` paths in `docker-compose.yml` to point to the correct service directories.

---

## 3. Minikube Cannot Access Local Docker Images

### Issue

Pods entered:

```
ErrImageNeverPull
```

### Root Cause

The Deployment used:

```yaml
imagePullPolicy: Never
```

but the image was not available inside the Minikube cluster.

### Solution

Loaded local images into Minikube:

```bash
minikube image load microservices-user-service:latest
minikube image load microservices-product-service:latest
minikube image load microservices-order-service:latest
minikube image load microservices-gateway-service:latest
```

Verified using:

```bash
minikube image ls
```

---

## 4. Container Restart Issue (`CrashLoopBackOff`)

### Issue

Some pods repeatedly restarted:

```
CrashLoopBackOff
```

### Root Cause

The application container started but failed during runtime due to application configuration or health check failures.

### Solution

Investigated using:

```bash
kubectl logs <pod-name>
```

and:

```bash
kubectl describe pod <pod-name>
```

Fixed application configuration and verified readiness/liveness probe settings.

---

## 5. Readiness and Liveness Probe Failures

### Issue

Pods were running but not becoming ready.

### Root Cause

The health check path configured in Kubernetes did not match the application's available endpoint.

Example:

Incorrect:

```yaml
path: /
```

Correct:

```yaml
path: /health
```

### Solution

Updated probes to match the application's health endpoint:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 3000

livenessProbe:
  httpGet:
    path: /health
    port: 3000
```

---

## 6. Multiple Old ReplicaSets and Pods

### Issue

Old failed pods remained after updating deployments.

### Root Cause

Kubernetes maintains previous ReplicaSets during deployment updates.

### Solution

Removed old deployments and redeployed:

```bash
kubectl delete deployment <deployment-name>

kubectl apply -f deployments/
```

---

## 7. Service Communication Validation

### Issue

Verifying communication between microservices inside Kubernetes.

### Solution

Used Kubernetes service DNS names instead of pod IP addresses:

Example:

```
http://user-service:3000
http://product-service:3001
http://order-service:3002
```

Tested communication from inside the cluster using:

```bash
kubectl exec -it <gateway-pod-name> -- sh
```

and:

```bash
curl http://user-service:3000
```

---

# Key Learnings

* Kubernetes Services provide stable networking between dynamic Pods.
* Docker images must be available inside the Kubernetes cluster.
* Deployment image names must exactly match available images.
* Readiness and liveness probes are important for application reliability.
* Kubernetes debugging commands like `kubectl logs` and `kubectl describe pod` help identify deployment issues quickly.









# Cleanup

Delete Kubernetes resources:

```bash
kubectl delete -f deployments/

kubectl delete -f services/
```

Stop Minikube:

```bash
minikube stop
```

---

# Conclusion

The microservices application has been successfully containerized and deployed on Kubernetes using Minikube with:

* Kubernetes Deployments
* ClusterIP Services
* Internal service discovery
* Health probes
* Resource configuration
* Service communication testing

```
```
