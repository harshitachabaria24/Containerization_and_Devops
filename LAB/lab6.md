Experiment 6: Multi-Container Application using Docker Compose (WordPress + MySQL)
🎯 1. Objective

To deploy a multi-container application using Docker Compose consisting of:

WordPress (frontend application)
MySQL (database backend)

Also to:

Understand container networking
Use persistent volumes
Learn service scaling
Compare Docker Compose with Docker Swarm
🧰 2. Prerequisites
Docker installed and running
Docker Compose available
Basic understanding of containers
🏗️ 3. Architecture Overview
User (Browser)
      |
   WordPress Container
      |
   MySQL Container
      |
   Persistent Volume (Storage)
WordPress connects to MySQL using service name (db)
Data persists using Docker volumes
📁 4. Implementation Steps
🔹 Step 1: Create Project Directory
mkdir wp-compose-lab
cd wp-compose-lab

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115152.png)

🔹 Step 2: Create docker-compose.yml
services:
  db:
    image: mysql:5.7
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
    depends_on:
      - db
    ports:
      - "8081:80"
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

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115210.png)

🔹 Step 3: Start the Application
docker compose up -d

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115300.png)

🔹 Step 4: Verify Running Containers
docker ps

Expected:

WordPress container
MySQL container

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115340.png)

🔹 Step 5: Access WordPress

Open in browser:

http://localhost:8081

👉 Output:

WordPress installation/setup page

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115420.png)

🔹 Step 6: Verify Volumes
docker volume ls

👉 Volumes:

db_data
wp_data

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115500.png)

🔹 Step 7: Stop the Application
docker compose down

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115530.png)

🔄 5. Scaling in Docker Compose
🔹 Step 8: Scale WordPress Service
docker compose up -d --scale wordpress=3

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115600.png)

🔹 Step 9: Verify Scaling
docker ps

👉 Multiple WordPress containers will be created

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115640.png)

⚠️ Observation
Only one container responds to requests
No load balancing in Docker Compose
💡 Reason

Docker Compose:

Does not provide load balancing
Cannot expose same port for multiple containers
🚀 6. Deployment using Docker Swarm
🔹 Step 10: Initialize Swarm
docker swarm init

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115710.png)

🔹 Step 11: Deploy Stack
docker stack deploy -c docker-compose.yml wpstack

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115750.png)

🔹 Step 12: Verify Services
docker service ls

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115820.png)

🔹 Step 13: Scale Service
docker service scale wpstack_wordpress=3

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115850.png)

🔹 Step 14: Verify Replicas
docker service ps wpstack_wordpress

📸 Screenshot:
(../screenshots/lab5/Screenshot%202026-04-25%20115920.png)

⚖️ 7. Docker Compose vs Docker Swarm
Feature	Docker Compose	Docker Swarm
Environment	Development	Production
Scaling	Limited	Built-in
Load Balancing	Not available	Available
Self-healing	No	Yes
Networking	Basic	Advanced
🌟 8. Advantages of Docker Swarm
Built-in load balancing
Automatic container restart
Easy scaling
Supports rolling updates
⚠️ 9. Limitations
Less popular than Kubernetes
Limited advanced features
Not ideal for large-scale systems
📚 10. Learning Outcomes
Understood multi-container deployment
Learned Docker networking and volumes
Observed scaling limitations in Compose
Implemented Swarm for better orchestration
📝 11. Conclusion

This experiment demonstrated how to deploy a WordPress + MySQL application using Docker Compose and highlighted its limitations in scaling. Docker Swarm provided a more advanced solution with built-in load balancing and better service management, making it suitable for production environments.