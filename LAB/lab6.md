# Experiment 6 A  
## Comparison of Docker Run and Docker Compose  

---

## PART B - PRACTICAL TASK

---

# Task 1: Single Container Comparison

## Step 1: Run Nginx Using Docker Run

### Command
```bash
docker run -d \
  --name lab-nginx \
  -p 8081:80 \
  -v $(pwd)/html:/usr/share/nginx/html \
  nginx:alpine

  Verify
docker ps

📸 Screenshot:
![Docker Run Output](../screenshots/lab6/Screenshot%202026-04-25%20140227.png)

Access
http://localhost:8081
Stop & Remove Container
docker stop lab-nginx
docker rm lab-nginx
Step 2: Run Using Docker Compose
Create docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8081:80"
    volumes:
      - ./html:/usr/share/nginx/html
Run
docker compose up -d
Verify
docker compose ps

📸 Screenshot:
![Docker Compose Output](../screenshots/lab6/docker-compose-nginx.png)

Stop
docker compose down
Task 2: Multi-Container Application
Objective

Deploy WordPress with MySQL using:

Docker Run (manual way)
Docker Compose (structured way)
A. Using Docker Run
Step 1: Create Network
docker network create wp-net
Step 2: Run MySQL
docker run -d \
  --name mysql \
  --network wp-net \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=wordpress \
  mysql:5.7
Step 3: Run WordPress
docker run -d \
  --name wordpress \
  --network wp-net \
  -p 8082:80 \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_PASSWORD=secret \
  wordpress:latest
Test
http://localhost:8082

📸 Screenshot:
![WordPress Docker Run](../screenshots/lab6/wp-docker-run.png)

B. Using Docker Compose
Create docker-compose.yml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
    volumes:
      - mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: secret
    depends_on:
      - mysql

volumes:
  mysql_data:
Run
docker compose up -d
Stop
docker compose down -v

📸 Screenshot:
![WordPress Docker Compose](../screenshots/lab6/wp-docker-compose.png)

PART C - CONVERSION & BUILD-BASED TASKS
Task 3: Convert Docker Run to Docker Compose
Problem 1: Basic Web Application
Given Docker Run
docker run -d \
  --name webapp \
  -p 5000:5000 \
  -e APP_ENV=production \
  -e DEBUG=false \
  --restart unless-stopped \
  node:18-alpine
Docker Compose Equivalent
version: '3.8'

services:
  webapp:
    image: node:18-alpine
    container_name: webapp
    ports:
      - "5000:5000"
    environment:
      APP_ENV: production
      DEBUG: "false"
    restart: unless-stopped
Run & Verify
docker compose up -d
docker compose ps

📸 Screenshot:
![WebApp Compose](../screenshots/lab6/webapp-compose.png)

Problem 2: Volume + Network Configuration
Docker Compose File
version: '3.8'

services:
  postgres-db:
    image: postgres:15
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - app-net

  backend:
    image: python:3.11-slim
    container_name: backend
    ports:
      - "8000:8000"
    environment:
      DB_HOST: postgres-db
      DB_USER: admin
      DB_PASS: secret
    depends_on:
      - postgres-db
    networks:
      - app-net

volumes:
  pgdata:

networks:
  app-net:
Run
docker compose up -d
Stop
docker compose down -v

📸 Screenshot:
![Backend + DB](../screenshots/lab6/backend-db.png)

Task 4: Resource Limits Conversion
Docker Compose
version: '3.8'

services:
  limited-app:
    image: nginx:alpine
    container_name: limited-app
    ports:
      - "9000:9000"
    restart: always
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 256M
Explanation
deploy works only in Docker Swarm mode
In normal Compose:
Limits are ignored
In Swarm:
Limits are enforced

📸 Screenshot:
![Resource Limits](../screenshots/lab6/resource-limits.png)

PART D - USING DOCKERFILE
Task 5: Replace Standard Image with Dockerfile
Step 1: Create app.js
const http = require('http');

http.createServer((req, res) => {
  res.end("Docker Compose Build Lab");
}).listen(3000);
Step 2: Create Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY app.js .

EXPOSE 3000

CMD ["node", "app.js"]
Step 3: Create docker-compose.yml
version: '3.8'

services:
  nodeapp:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: custom-node-app
    ports:
      - "3000:3000"
Run
docker compose up --build -d
Verify
http://localhost:3000

📸 Screenshot:
![Node App](../screenshots/lab6/node-app.png)

Difference: image vs build
Feature	image	build
Source	Docker Hub	Local Dockerfile
Control	Low	High
Customization	No	Yes
Task 6: Multi-Stage Dockerfile
Requirements
Multi-stage build
Smaller image size
Use Compose
Add environment variables
Add volume mount
Verify Image Size
docker images

📸 Screenshot:
![Multi Stage](../screenshots/lab6/multistage.png)

Experiment 6 B
Multi-Container Application (WordPress + MySQL)
Objective
Deploy WordPress + MySQL
Understand networking & volumes
Learn scaling
Compare with Swarm
Architecture
User → WordPress → MySQL → Volume
Step 1: Create Directory
mkdir wp-compose-lab
cd wp-compose-lab
Step 2: Create docker-compose.yml
version: '3.9'

services:
  db:
    image: mysql:5.7
    container_name: wordpress_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wordpress_app
    depends_on:
      - db
    ports:
      - "8080:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wp_data:/var/www/html

volumes:
  db_data:
  wp_data:
Step 3: Start Application
docker-compose up -d

📸 Screenshot:
![Compose Up](../screenshots/lab6/compose-up.png)

Step 4: Verify
docker ps

📸 Screenshot:
![Docker PS](../screenshots/lab6/docker-ps.png)

Step 5: Access WordPress
http://localhost:8080

📸 Screenshot:
![WordPress Setup](../screenshots/lab6/wp-setup.png)

Step 6: Check Volumes
docker volume ls

📸 Screenshot:
![Volumes](../screenshots/lab6/volumes.png)

Step 7: Stop
docker-compose down
Scaling in Docker Compose
docker-compose up --scale wordpress=3
Issues
Port conflict
No load balancing
Solution

Use Nginx Reverse Proxy

Docker Swarm
Initialize
docker swarm init
Deploy Stack
docker stack deploy -c docker-compose.yml wpstack
Scale
docker service scale wpstack_wordpress=3

📸 Screenshot:
![Swarm](../screenshots/lab6/swarm.png)

Benefits of Docker Swarm
Load balancing
Auto-healing
Scaling
Rolling updates
Limitations
Less popular than Kubernetes
Limited ecosystem
Key Learning Outcomes
Multi-container apps need orchestration
Docker Compose is best for:
Development
Testing
Learning