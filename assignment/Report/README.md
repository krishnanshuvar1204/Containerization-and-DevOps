# Project Deliverables

## 1. GitHub Repository

The complete project source code is available in the GitHub repository.

Repository includes:

* Backend source code
* Database configuration
* Dockerfiles
* Docker Compose configuration
* Documentation

🔗 **GitHub Repository:**  
[Containerized Web Application Project](https://github.com/krishnanshuvar1204/Containerization-and-DevOps/tree/main/assignment)

---

# 2. Separate Dockerfiles

The project contains separate Dockerfiles for each service.

### Backend Dockerfile

Location:

```
backend/Dockerfile
```

Responsible for building the Node.js backend API.

Features:

* Multi-stage build
* Alpine base image
* Non-root user
* Minimal runtime environment

---

### Database Dockerfile

Location:

```
database/Dockerfile
```

Responsible for building the PostgreSQL container with initialization scripts.

Environment variables configured:

```
POSTGRES_DB
POSTGRES_USER
POSTGRES_PASSWORD
```

---

# 3. docker-compose.yml

Docker Compose orchestrates all services.

Services defined:

* backend
* database

Compose configuration includes:

* static IP assignment
* external MACVLAN network
* named volumes
* environment variables
* restart policy


```yaml
services:

  db:
    build: ./database
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      mymacvlan:
        ipv4_address: 172.27.240.10
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d mydb"]
      interval: 10s
      retries: 5

  backend:
    build: ./backend
    container_name: backend_api
    restart: always
    ports:
      - "3000:3000"
    depends_on:
      db:
        condition: service_healthy
    environment:
      DB_HOST: 172.27.240.10
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      POSTGRES_DB: mydb
    networks:
      mymacvlan:
        ipv4_address: 172.27.240.11
      default:

networks:
  mymacvlan:
    external: true
  default:
    driver: bridge

volumes:
  pgdata:
```

---

# 4. Network Creation Command

The IPVLAN network is created manually using the following command:

```bash
docker network create -d macvlan \
--subnet=172.27.240.0/20 \
--gateway=172.27.240.1 \
-o parent=eth0 \
mymacvlan
```

---

# 5. Screenshot Proofs

The following screenshots are included as proof of correct configuration.

### docker network inspect

Command used:

```
docker network inspect mynet
```

![net](../Screenshots/Screenshot(941).png)

Shows:

* network configuration
* connected containers
* assigned IP addresses

---

### Container IPs

Command used:

```
docker inspect backend_api
```
![backend](../images/backend.png)


```
docker inspect postgres_db
```

![database](../Screenshots/Screenshot(943).png)

Shows static IP assignment for containers.

---

### Volume Persistence Test

Add content to the database:

![BeforeDown](../Screenshots/Screenshot(942).png)


Commands used:

```
docker compose down
docker compose up -d
```

![down](../Screenshots/Screenshot(949).png)

![AfterDown](../Screenshots/Screenshot(949).png)

Previously inserted data remains available after container restart.

---

# 6. Short Report (3–5 Pages)

The report contains the following sections.

---

## Build Optimization Explanation

The backend container uses a **multi-stage Docker build with an Alpine Linux base image** to reduce the final image size and improve security. Multi-stage builds separate the **build environment** from the **runtime environment**, ensuring that only the necessary application files and dependencies are included in the final container image.

### Backend Multi-Stage Dockerfile

```dockerfile
# Builder stage
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# Runtime stage
FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app .

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 3000

CMD ["node","server.js"]
```

### Optimization Techniques Used

* **Alpine base images** provide a lightweight Linux environment.
* **Multi-stage builds** ensure that only runtime files are included in the final image.
* **Non-root user execution** improves container security.
* **Minimal layers** reduce overall image size.

These optimizations help produce a **smaller, more secure, and efficient container image** compared to traditional single-stage Docker builds.


---

## Network Design Diagram

Architecture:

```
Client
  │
  ▼
Backend Container
  │
  ▼
PostgreSQL Container
```

All services communicate through a custom IPVLAN network.

---

## Image Size Comparison

Docker images were compared between optimized and non-optimized builds.

Create one stack with Alpine and multi-stage dockerfile named as opimizedsize_backend and other with no Alpine and single-stage named as normal_backend.

```
docker images
```

![size](../Screenshots/Screenshot(952).png)


We can see that opimizedsize_backend has less size than normal_backend.

Results show that Alpine-based images significantly reduce container size.

---

## macvlan vs ipvlan Comparison

| Feature            | Macvlan                     | Ipvlan                   |
| ------------------ | --------------------------- | ------------------------ |
| MAC Address        | Unique                      | Shared                   |
| Host communication | No                          | Yes                      |
| Network isolation  | High                        | Moderate                 |
| Use case           | Physical network simulation | Virtualized environments |

IPVLAN was chosen due to compatibility with WSL2 networking.