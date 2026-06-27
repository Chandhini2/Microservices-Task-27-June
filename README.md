# Microservices-Task

## Overview
This document provides details on testing various services after running the `docker-compose` file. These services include User, Product, Order, and Gateway Services. Each service has its own endpoints for testing purposes.

---

## Services and Endpoints

### **User Service**
- **Base URL:** `http://localhost:3000`
- **Endpoints:**
  - **List Users:**  
    ```
    curl http://localhost:3000/users
    ```
    Or open in your browser: [http://localhost:3000/users](http://localhost:3000/users)

---

### **Product Service**
- **Base URL:** `http://localhost:3001`
- **Endpoints:**
  - **List Products:**  
    ```
    curl http://localhost:3001/products
    ```
    Or open in your browser: [http://localhost:3001/products](http://localhost:3001/products)

---

### **Order Service**
- **Base URL:** `http://localhost:3002`
- **Endpoints:**
  - **List Orders:**  
    ```
    curl http://localhost:3002/orders
    ```
    Or open in your browser: [http://localhost:3002/orders](http://localhost:3002/orders)

---

### **Gateway Service**
- **Base URL:** `http://localhost:3003/api`
- **Endpoints:**
  - **Users:**  
    ```
    curl http://localhost:3003/api/users
    ```
  - **Products:**  
    ```
    curl http://localhost:3003/api/products
    ```
  - **Orders:**  
    ```
    curl http://localhost:3003/api/orders
    ```

---

## Instructions
1. Start all services using the `docker-compose` file:
   ```
   docker-compose up
   ```
2. Once the services are running, use the above endpoints to verify the functionality.

Happy testing!


Solutions:

# Node.js Microservices Containerization using Docker Compose

## Project Overview

This project demonstrates containerization and orchestration of Node.js microservices using Docker and Docker Compose.

The application consists of multiple independent services that are packaged into Docker containers and managed together using Docker Compose.

## Microservices Architecture

The project contains the following services:

| Service         | Port |
| --------------- | ---- |
| User Service    | 3000 |
| Product Service | 3001 |
| Order Service   | 3002 |
| Gateway Service | 3003 |

Each service has its own Dockerfile and runs independently inside a Docker container.

---

# 1. Dockerfile Creation

A separate Dockerfile was created for each microservice.

Each Dockerfile includes:

* Node.js 20 Alpine base image
* Working directory configuration
* Dependency installation
* Application source code copy
* Port exposure
* Application startup command

## Example Dockerfile

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3002

CMD ["node", "app.js"]
```

## Dockerfile Explanation

### Base Image

```dockerfile
FROM node:20-alpine
```

Uses Node.js version 20 with Alpine Linux.

Alpine image is lightweight and reduces container size.

---

### Working Directory

```dockerfile
WORKDIR /app
```

Creates and sets `/app` as the working directory inside the container.

All further commands execute from this location.

---

### Copy Dependencies

```dockerfile
COPY package*.json ./
```

Copies:

* package.json
* package-lock.json

into the container.

---

### Install Dependencies

```dockerfile
RUN npm install
```

Installs all required Node.js packages.

---

### Copy Application Code

```dockerfile
COPY . .
```

Copies the complete service source code into the container.

---

### Expose Port

Example:

```dockerfile
EXPOSE 3002
```

Indicates the port where the service listens.

---

### Start Application

```dockerfile
CMD ["node", "app.js"]
```

Starts the Node.js application when the container starts.

---


<img width="940" height="401" alt="image" src="https://github.com/user-attachments/assets/7225646e-ed51-4ecb-a181-fe50055dad66" />


# 2. Docker Compose Configuration

A `docker-compose.yml` file was created to manage all services together.

Docker Compose provides:

* Multiple container management
* Network creation
* Port mapping
* Service communication

## docker-compose.yml

```yaml
version: "3.8"

services:

  user-service:
    build: ./user-service
    container_name: user-service
    ports:
      - "3000:3000"
    networks:
      - microservices-network


  product-service:
    build: ./product-service
    container_name: product-service
    ports:
      - "3001:3001"
    networks:
      - microservices-network


  order-service:
    build: ./order-service
    container_name: order-service
    ports:
      - "3002:3002"
    networks:
      - microservices-network


  gateway-service:
    build: ./gateway-service
    container_name: gateway-service
    ports:
      - "3003:3003"
    depends_on:
      - user-service
      - product-service
      - order-service
    networks:
      - microservices-network


networks:

  microservices-network:
    driver: bridge
```



<img width="940" height="474" alt="image" src="https://github.com/user-attachments/assets/12b87836-d95d-40bf-a53c-eb52cf47598f" />


---

# 3. Running the Application Locally

## Build and Start Containers

Run:

```bash
docker-compose up --build
```

Docker will:

1. Create images
2. Create containers
3. Create network
4. Start all services

---


<img width="940" height="206" alt="image" src="https://github.com/user-attachments/assets/99036b6b-6ed3-4f12-9a35-129fa8f124b0" />


<img width="940" height="250" alt="image" src="https://github.com/user-attachments/assets/f23f8326-d97c-48d0-8a64-b2cc89517fdd" />


<img width="940" height="246" alt="image" src="https://github.com/user-attachments/assets/0aa42375-de84-40ec-ab4b-0f4dae99c297" />


<img width="940" height="259" alt="image" src="https://github.com/user-attachments/assets/39dd6a99-804d-4332-826e-f2f84a4ad4bb" />


<img width="940" height="286" alt="image" src="https://github.com/user-attachments/assets/63e15866-40e9-4e3d-8fa8-571445a8e25e" />


<img width="940" height="272" alt="image" src="https://github.com/user-attachments/assets/fde5ee23-d335-46ca-a24b-a1757bd500c6" />


<img width="940" height="268" alt="image" src="https://github.com/user-attachments/assets/a300cd7f-c980-47d1-b672-bd8446c3a573" />



<img width="940" height="291" alt="image" src="https://github.com/user-attachments/assets/0c16d840-5051-44e2-8c0f-96b365230065" />




## Check Running Containers

Command:

```bash
docker ps
```

Expected output:

```
user-service
product-service
order-service
gateway-service
```

---


<img width="940" height="241" alt="image" src="https://github.com/user-attachments/assets/e2c617f8-5934-49dc-9125-f58a097e1c81" />



## View Logs

To check service logs:

```bash
docker logs user-service
```

Example:

```
User Service running on port 3000
```

Product service:

```bash
docker logs product-service
```

Order service:

```bash
docker logs order-service
```

Gateway service:

```bash
docker logs gateway-service
```

---

# 4. Testing & Validation

After successful startup, services can be accessed using localhost.

## User Service

```
http://localhost:3000
```

Expected:

User service response

## Product Service

```
http://localhost:3001
```

Expected:

Product service response

## Order Service

```
http://localhost:3002
```

## Gateway Service

```
http://localhost:3003
```

Gateway communicates with other services through Docker network.

---

# Docker Network Communication

Docker Compose creates a shared network:

```
microservices-network
```

All containers communicate internally using service names.

Example:

```
gateway-service

        |
        |
        v

user-service
product-service
order-service
```

The gateway does not need container IP addresses because Docker DNS resolves service names automatically.

---

# Screenshots

## Docker Desktop Containers

<img width="940" height="202" alt="image" src="https://github.com/user-attachments/assets/6e8afc0a-08e9-44d3-977a-d66b64c6a77a" />


Shows:

* user-service running
* product-service running
* order-service running
* gateway-service running

## Docker Compose Output

<img width="940" height="206" alt="image" src="https://github.com/user-attachments/assets/c37fff96-c480-4def-9a79-b5d8a6751d89" />


## Docker Logs

<img width="940" height="386" alt="image" src="https://github.com/user-attachments/assets/29b0a686-f883-4bfa-99cd-2fe68dc42f2a" />


## Final Output Testing

(Add browser/Postman screenshots here)

Example:

User:

```
localhost:3000
```

<img width="940" height="180" alt="image" src="https://github.com/user-attachments/assets/13f5755f-9327-4ca5-b861-50269571066f" />


Product:

```
localhost:3001
```

<img width="940" height="148" alt="image" src="https://github.com/user-attachments/assets/327a9eba-2e86-4344-b736-7e5618764d33" />

Order:

```
localhost:3002
```

<img width="940" height="145" alt="image" src="https://github.com/user-attachments/assets/9b3549ba-df7d-4065-86e7-d7812a15d1bd" />

Gateway:

```
localhost:3003
```


<img width="940" height="188" alt="image" src="https://github.com/user-attachments/assets/1c546674-6b54-4da6-bde9-b40401d4d19a" />

---

# Challenges Faced

## Issue: Container was not starting properly

Initially all Dockerfiles had:

```dockerfile
CMD ["npm", "Start"]
```

After changing it to:

```dockerfile
CMD ["node", "app.js"]
```

all services started successfully.

---

# Technical Reason

Docker executes the CMD instruction exactly as provided.

Initially Docker executed:

```
npm Start
```

The problem was:

* NPM script names are case-sensitive.
* The correct npm lifecycle command is:

```
npm start
```

not:

```
npm Start
```

Because the script name did not match, npm could not execute the application startup script correctly.

---

## Previous Flow

```
Docker Container

        |
        v

npm Start

        |
        v

package.json scripts

        |
        v

node app.js
```

The application depended on npm configuration.

---

## Updated Flow

After changing CMD:

```dockerfile
CMD ["node","app.js"]
```

Docker directly starts Node.js:

```
Docker Container

        |
        v

Node Runtime

        |
        v

app.js

        |
        v

Application Starts
```

This removed dependency on npm scripts and directly executed the application entry point.

---

<img width="940" height="228" alt="image" src="https://github.com/user-attachments/assets/9415a804-acd6-4b42-8a43-0c75acca3c1a" />


# Troubleshooting

## 1. Container exits immediately

Check logs:

```bash
docker logs container-name
```

Possible reasons:

* Incorrect CMD
* Application error
* Missing dependencies

---

## 2. Port already in use

Check running containers:

```bash
docker ps
```

Stop container:

```bash
docker stop container-name
```

---

## 3. Rebuild containers after changes

Run:

```bash
docker-compose down

docker-compose up --build
```

---


<img width="949" height="403" alt="image" src="https://github.com/user-attachments/assets/5991222f-26c9-477a-980a-e971738c2fd6" />



## 4. Check container status

```bash
docker ps -a
```

<img width="857" height="266" alt="image" src="https://github.com/user-attachments/assets/ec511f99-a5b8-43d8-b461-deaa4bbb774c" />


---

# Conclusion

The Node.js microservices were successfully containerized using Dockerfiles and orchestrated using Docker Compose.

All services were:

* Built successfully
* Running inside containers
* Connected through Docker network
* Accessible through localhost ports

The final architecture provides a scalable and isolated microservices deployment environment.



### Folder Structure

# Folder Structure

```
Microservices-Task-27-June/

├── user-service/
│   └── Dockerfile
│
├── product-service/
│   └── Dockerfile
│
├── order-service/
│   └── Dockerfile
│
├── gateway-service/
│   └── Dockerfile
│
├── docker-compose.yml
│
└── README.md
```

