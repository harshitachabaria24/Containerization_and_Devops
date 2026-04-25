# 🐳 Containerization and DevOps

## Experiment 5: Docker – Volumes, Environment Variables, Monitoring & Networks

---

## 📌 Objective

To understand and implement core Docker concepts including volumes, environment variables, monitoring, and networking, and to build a real-world multi-container application.

---

# 🔹 Part 1: Docker Volumes – Persistent Data Storage

## 🧪 Lab 1: Understanding Data Persistence

### Problem: Container Data is Ephemeral

```bash
docker run -it --name test-container ubuntu /bin/bash
```

Inside container:

```bash
echo "Hello World" > /data/message.txt
cat /data/message.txt
exit
```

Restart container:

```bash
docker start test-container
docker exec test-container cat /data/message.txt
```

👉 Data is lost after restart.

📸 Screenshot:
![Persistence](screenshots/Screenshot 2026-04-24 114256.png)

---

## 🧪 Lab 2: Volume Types

### 1. Anonymous Volume

```bash
docker run -d -v /app/data --name web1 nginx
```

📸 Screenshot:
![Anonymous Volume](screenshots/Screenshot 2026-04-24 114256.png)

---

### 2. Named Volume

```bash
docker volume create mydata
docker run -d -v mydata:/app/data --name web2 nginx
```

📸 Screenshot:
![Named Volume](screenshots/Screenshot 2026-04-24 114339.png)

---

### 3. Bind Mount

```bash
docker run -d -v /mnt/c/Users/HARSHITA/myapp-data:/app/data --name web3 nginx
```

📸 Screenshot:
![Bind Mount](screenshots/Screenshot 2026-04-24 114546.png)

---

## 🧪 Lab 3: Volume Commands

```bash
docker volume ls
docker volume create app-volume
docker volume inspect app-volume
docker volume prune
docker volume rm app-volume
docker cp local-file.txt web3:/app/data/
```

📸 Screenshot:
![Volume Commands](screenshots/Screenshot 2026-04-24 114617.png)

---

# 🔹 Part 2: Environment Variables

## 🧪 Lab 1: Using -e Flag

```bash
docker run -d \
  --name app1 \
  -e DEBUG=true \
  nginx
```


---

## 🧪 Lab 2: Using .env File

```bash
docker run -d --env-file .env --name app2 nginx
```

📸 Screenshot:
![Env File](screenshots/lab5/Screenshot 2026-04-24 114617.png)

---

## 🧪 Lab 3: Flask App with Environment Variables

```bash
docker run -d \
  --name flask-app \
  -p 5000:5000 \
  -e DATABASE_HOST=prod-db \
  flask-app
```

📸 Screenshot:
![Flask Env](screenshots/Screenshot 2026-04-24 114708.png)

---

# 🔹 Part 3: Docker Monitoring

## 🧪 Lab 1: docker stats

```bash
docker stats
```

📸 Screenshot:
![Stats](screenshots/Screenshot 2026-04-24 114736.png)

---

## 🧪 Lab 2: docker top

```bash
docker top flask-app
```
---

## 🧪 Lab 3: docker logs

```bash
docker logs -f flask-app
```
---

## 🧪 Lab 4: docker inspect

```bash
docker inspect flask-app
```

📸 Screenshot:
![Inspect](screenshots/Screenshot 2026-04-24 114804.png)

---

## 🧪 Lab 5: docker events

```bash
docker events
```

📸 Screenshot:
![Events](screenshots/Screenshot 2026-04-24 114804.png)
![Events](screenshots/Screenshot 2026-04-24 114820.png)


---

# 🔹 Part 4: Docker Networks

## 🧪 Lab 1: List Networks

```bash
docker network ls
```

📸 Screenshot:
![Network List](screenshots/Screenshot 2026-04-24 114857.png)

---

## 🧪 Lab 2: Bridge Network

```bash
docker network create my-network
docker run -d --name web1 --network my-network nginx
docker run -d --name web2 --network my-network nginx
```

📸 Screenshot:
![Bridge](screenshots/Screenshot 2026-04-24 114906.png)

---

## 🧪 Lab 3: Network Commands

```bash
docker network inspect my-network
docker network connect my-network web1
docker network disconnect my-network web1
```

📸 Screenshot:
![Network Commands](screenshots/Screenshot 2026-04-24 114916.png)

---

# 🔹 Part 5: Real-World Multi-Container Application

## 🏗️ Architecture

* Flask App → Port 5000
* PostgreSQL → Port 5432
* Redis → Port 6379
* Custom Network

---

## ⚙️ Implementation

```bash
docker network create myapp-network
```

```bash
docker run -d --name postgres --network myapp-network -e POSTGRES_PASSWORD=mysecretpassword postgres:15
```

```bash
docker run -d --name redis --network myapp-network redis:7-alpine
```

```bash
docker run -d \
  --name flask-app \
  --network myapp-network \
  -p 5000:5000 \
  -e DATABASE_URL="postgresql://postgres:mysecretpassword@postgres:5432/mydatabase" \
  -e REDIS_URL="redis://redis:6379" \
  flask-app
```

📸 Screenshot:
![Final Setup](screenshots/Screenshot 2026-04-24 115107.png)
![Final Setup](screenshots/Screenshot 2026-04-24 115129.png)
![Final Setup](screenshots/Screenshot 2026-04-24 115152.png)

---

## 🔍 Verification

```bash
docker ps
docker stats
docker logs -f flask-app
docker network inspect myapp-network
```
---

# 📊 Result

Successfully implemented Docker concepts including volumes, environment variables, monitoring, and networking, and deployed a real-world multi-container application.

---

# 📌 Key Takeaways

* Docker volumes ensure data persistence
* Environment variables provide flexible configuration
* Monitoring tools help debug containers
* Networks enable container communication
* Multi-container applications simulate real-world systems

---
