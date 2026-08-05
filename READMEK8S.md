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

<img width="1694" height="439" alt="image" src="https://github.com/user-attachments/assets/582ee2a5-ef0c-434b-acde-74dc02d5604c" />


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

<img width="940" height="256" alt="image" src="https://github.com/user-attachments/assets/e2ada74f-540b-43ea-9fcf-2b5cab8873d3" />




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

<img width="940" height="295" alt="image" src="https://github.com/user-attachments/assets/8ad10b1d-3bf6-4fbb-9e1a-e20feebf218b" />


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

<img width="1755" height="535" alt="image" src="https://github.com/user-attachments/assets/f527225d-7f62-42b7-b9bb-e1cbbbdf73f5" />


<img width="940" height="478" alt="image" src="https://github.com/user-attachments/assets/66a70446-3d2b-4d5d-860d-d237b747ed46" />


---

# Kubernetes Deployment

## Deploy Microservices

Apply Deployment manifests:

```bash
kubectl apply -f deployments/
```

<img width="940" height="75" alt="image" src="https://github.com/user-attachments/assets/8581caf8-7109-40ee-88be-370f09ed2794" />

<img width="940" height="230" alt="image" src="https://github.com/user-attachments/assets/a1cd986f-1b6a-4a36-945e-ffe3eeefc66b" />


<img width="940" height="190" alt="image" src="https://github.com/user-attachments/assets/32167bed-33f1-4ce3-8799-d8b245cb1af1" />


<img width="940" height="253" alt="image" src="https://github.com/user-attachments/assets/b20621dc-8079-4242-a647-47cea752065a" />



Apply Service manifests:

```bash
kubectl apply -f services/
```

<img width="940" height="183" alt="image" src="https://github.com/user-attachments/assets/160b1ba9-c146-4f78-b0ef-8bf1cae58e08" />



<img width="940" height="400" alt="image" src="https://github.com/user-attachments/assets/5b2232fe-7e0e-416b-b36d-29384a94c368" />


---

# Verify Deployments

Check deployments:

```bash
kubectl get deployments
```

<img width="940" height="253" alt="image" src="https://github.com/user-attachments/assets/43038b6e-57db-4141-b1d4-d34712b17782" />


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


<img width="940" height="493" alt="image" src="https://github.com/user-attachments/assets/e76cc575-7d04-4090-b2c3-7b4621662673" />


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


<img width="1736" height="716" alt="image" src="https://github.com/user-attachments/assets/1d5e81ce-6cb2-4903-baf9-3fef8a213879" />



# Viewing Logs

View Gateway logs:

```bash
kubectl logs deployment/gateway-service
```

View individual pod logs:

```bash
kubectl logs <pod-name>
```

<img width="940" height="143" alt="image" src="https://github.com/user-attachments/assets/00479ab6-3f46-4004-bb35-1cfb213cffe6" />


---

# Troubleshooting

## Check Pod Errors

```bash
kubectl describe pod <pod-name>
```
<img width="1888" height="557" alt="image" src="https://github.com/user-attachments/assets/1920549c-797b-4de9-9276-84f99fc43a6b" />



<img width="1520" height="826" alt="image" src="https://github.com/user-attachments/assets/1d6a966b-fe6d-422d-8232-c95689fb0b1a" />

---

## Check Container Logs

```bash
kubectl logs <pod-name>
```
<img width="1835" height="136" alt="image" src="https://github.com/user-attachments/assets/21593b72-1337-40aa-9729-0d6f7635099e" />

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

<img width="955" height="470" alt="image" src="https://github.com/user-attachments/assets/a9f9cc54-a87d-4fd7-969b-435caaea1f48" />


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
│
└── READMEK8S.md
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
<img width="940" height="55" alt="image" src="https://github.com/user-attachments/assets/eec4a25b-7658-471a-b2e5-ed0d4f0d8663" />

```

---
                                 
3. Service testing

Command:

```bash
kubectl port-forward service/gateway-service 3003:3003
```

<img width="1404" height="83" alt="image" src="https://github.com/user-attachments/assets/812bbe01-f7d9-4f0e-a204-8fd279672b97" />



Screenshot:

```
<img width="940" height="169" alt="image" src="https://github.com/user-attachments/assets/3d40b483-9ec2-4e6f-bc7d-a6c16ae36cff" />

```
<img width="940" height="169" alt="image" src="https://github.com/user-attachments/assets/49c7ff90-f167-4a86-aac7-68cd5e051790" />


<img width="940" height="241" alt="image" src="https://github.com/user-attachments/assets/9c43c295-0fa7-4612-835c-5e6bb8f88204" />


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


<img width="940" height="499" alt="image" src="https://github.com/user-attachments/assets/218cab12-bd71-4904-a775-8993fb024efe" />



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



<img width="940" height="456" alt="image" src="https://github.com/user-attachments/assets/1c18639b-b5c8-4ea2-91dc-7e578b70641f" />




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
