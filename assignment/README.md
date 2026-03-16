# Containerized Web Application with PostgreSQL using Docker Compose and Macvlan/IPVLAN

## Overview

This project demonstrates a **containerized web application** built using Docker and Docker Compose.

The system consists of a **backend API and a PostgreSQL database**, deployed as separate containers and connected using **Macvlan/IPVLAN networking with static IP addresses**.

The project demonstrates several **production-ready containerization practices**, including:

* Multi-stage Docker builds
* Minimal Alpine base images
* Non-root container execution
* Docker Compose service orchestration
* Persistent database storage using Docker volumes
* Health checks for service readiness
* Static IP assignment using Macvlan/IPVLAN networking

This project fulfills the requirements of the **Containerization and DevOps Assignment**.

---

# Architecture

```
Client Browser / Postman
        │
        ▼
Backend API Container (Node.js + Express)
172.27.240.11
        │
        ▼
PostgreSQL Database Container
172.27.240.10
```

Both containers communicate over a **custom Macvlan/IPVLAN network**.

---

# Project Structure

```
containerized-webapp
│
├── backend
│   ├── Dockerfile
│   ├── server.js
│   ├── package.json
│   └── .dockerignore
│
├── database
│   └── Dockerfile
│
├── docker-compose.yml
├── README.md
└── report
    └── report.pdf
```

---

# Technology Stack

| Component        | Technology           |
| ---------------- | -------------------- |
| Backend API      | Node.js + Express    |
| Database         | PostgreSQL           |
| Containerization | Docker               |
| Orchestration    | Docker Compose       |
| Networking       | Macvlan / IPVLAN     |
| Storage          | Docker Named Volumes |

---

# Functional API Endpoints

### Health Check

```
GET /health
```

Example:

```
http://localhost:3000/health
```

Response:

```
OK
```

---

### Insert Record

```
POST /records
```

Example request body:

```
{
"name": "example"
}
```
![posting records](Screenshots/Screenshot(944).png)
![posting records](Screenshots/Screenshot(945).png)

---

### Fetch Records

```
GET /records
```
![getting records](Screenshots/Screenshot(946).png)
Returns all stored records from the database.


---

# Docker Image Optimization

The backend container uses **multi-stage Docker builds** and minimal images to reduce image size and improve security.

### Optimized Build Features

* Alpine base image
* Multi-stage build
* Non-root user execution
* Reduced image layers
* `.dockerignore` usage

These practices result in **smaller image sizes and faster deployments**.

---

# Create Network (Required)

Create the Macvlan/IPVLAN network manually before running Docker Compose.

Example:

```bash
docker network create -d macvlan \
--subnet=172.27.240.0/20 \
--gateway=172.27.240.1 \
-o parent=eth0 \
mymacvlan
```
![creating network](Screenshots/Screenshot(935).png)
Verify the network:

```bash
docker network inspect mymacvlan
```
![inspecting network](Screenshots/Screenshot(941).png)
---

# Build and Run the Application

### Build Containers

```
docker compose build
```

### Start Containers

```
docker compose up --build
```
![compose up](Screenshots/Screenshot(947).png)
![compose up](Screenshots/Screenshot(948).png)
### Run in Background


---

# Verify Containers

```
docker ps
```
![running containers](Screenshots/Screenshot(951).png)
Expected containers:

* backend_api
* postgres_db

---

# Test API

Open in browser or Postman:

```
http://localhost:3000/health
```

Expected output:

```
OK
```
![Localhost Response](Screenshots/Screenshot(940).png)
---

# Volume Persistence

The PostgreSQL container uses a **Docker named volume** to persist data.

Stop containers:

```
docker compose down
```
![compose down](Screenshots/Screenshot(949).png)
Restart:

```
docker compose up
```
![compose up](Screenshots/Screenshot(949).png)
The database records will still exist, demonstrating **persistent storage**.

---

# Networking

The application uses **Macvlan/IPVLAN networking** which assigns containers **static IP addresses on the local network**.

Benefits include:

* Direct LAN access
* Improved network isolation
* Realistic deployment environment

However, Macvlan networks may restrict **host-to-container communication**, which is an important networking concept demonstrated in this project.

---

# Key Features

* Backend API containerized using Docker
* PostgreSQL database container
* Multi-stage Docker builds
* Healthcheck implementation
* Docker Compose orchestration
* Static IP addressing
* Persistent database storage
* Macvlan/IPVLAN container networking

---

# Conclusion

This project demonstrates a **production-style containerized architecture** using Docker and Docker Compose.

It highlights important DevOps concepts including **container orchestration, persistent storage, optimized Docker builds, and advanced container networking techniques**.

The system is scalable, portable, and easily deployable across environments.
