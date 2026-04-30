# Experiment 11

## Container Orchestration with Docker Stack

---

# Objective

To understand and implement container orchestration using Docker Stack and Docker Swarm for deploying and managing multi-container applications across a cluster.

---

# Theory

## Docker Stack

Docker Stack is a collection of services that can be deployed together using a `docker-compose.yml` file (version 3+). It enables orchestration and scaling on Docker Swarm.

---

## Docker Swarm

Docker Swarm is Docker’s native clustering tool that turns multiple Docker hosts into a single virtual system.

### Components

* **Manager Node** → Manages cluster and scheduling
* **Worker Node** → Executes tasks
* **Service** → Definition of containers
* **Task** → Running container instance

---

## Key Concepts

| Concept         | Description                 |
| --------------- | --------------------------- |
| Stack           | Group of services           |
| Service         | Container definition        |
| Replica         | Number of instances         |
| Overlay Network | Communication between nodes |
| Volume          | Persistent storage          |

---

# Prerequisites

* Docker installed
* Docker Compose v3+
* Basic Docker knowledge
* Terminal access

---

# Environment Setup

## Check Docker Version

```bash id="checkversion"
docker --version
docker-compose --version
```

---

## Initialize Swarm

```bash id="swarminit"
docker swarm init
```

📸 Screenshot:
`![Swarm Init](../screenshots/lab11/Screenshot%20(748).png)

`![Swarm Init](../screenshots/lab11/Screenshot%20(749).png)

---

## Verify Swarm

```bash id="verifyswarm"
docker info | grep -i swarm
docker node ls
```

📸 Screenshot:
`![Node List](../screenshots/lab11/Screenshot%20(749).png)

---

# Experiment Steps

---

## Step 0: docker-compose.yml

📸 Screenshot:
`![Compose File](../screenshots/lab11/Screenshot%20(750).png)

---

## Step 1: Stop Existing Containers

```bash id="stopcontainers"
docker compose down
docker ps
```
---

## Step 2: Initialize Swarm

```bash id="init"
docker swarm init
```

## Step 3: Verify Nodes

```bash id="nodes"
docker node ls
```

📸 Screenshot:
`![Nodes](../screenshots/lab11/Screenshot%20(749).png)

---

## Step 4: Deploy Stack

```bash id="deploy"
docker stack deploy -c docker-compose.yml wpstack
```

📸 Screenshot:
`![Stack Deploy](../screenshots/lab11/Screenshot%20(750).png)

---

## Step 5: List Services

```bash id="services"
docker service ls
```

📸 Screenshot:
`![Service List](../screenshots/lab11/Screenshot%20(750).png)

---

## Step 6: Inspect Service Tasks

```bash id="tasks"
docker service ps wpstack_wordpress
```

📸 Screenshot:
`![Service Tasks](../screenshots/lab11/Screenshot%20(751).png)

---

## Step 7: Check Running Containers

```bash id="containers"
docker ps
```
---

## Step 8: Scale Service

```bash id="scale"
docker service scale wpstack_wordpress=3
```

---

## Step 9: Filter WordPress Containers

```bash id="filter"
docker ps | grep wordpress
```

📸 Screenshot:
`![Filtered Containers](../screenshots/lab11/Screenshot%20(751).png)

---

## Step 10: Remove Stack

```bash id="remove"
docker stack rm wpstack
```

📸 Screenshot:
`![Stack Remove](../screenshots/lab11/Screenshot%20(752).png)

---

# Observations

| Parameter              | Observed Value |
| ---------------------- | -------------- |
| Stack Name             | wpstack        |
| Services Deployed      | ___            |
| Initial Replicas       | ___            |
| Replicas After Scaling | 3              |
| WordPress Port         | ___            |
| MySQL Service Name     | ___            |
| Overlay Network        | ___            |

---

# Expected Output

* `docker stack ls` → shows stack
* `docker service ls` → shows services
* WordPress accessible on browser

---

# Docker Stack vs Docker Compose

| Feature        | Docker Compose | Docker Stack |
| -------------- | -------------- | ------------ |
| Scope          | Single host    | Multi-node   |
| Swarm Required | No             | Yes          |
| Scaling        | Manual         | Declarative  |
| Load Balancing | No             | Yes          |
| Updates        | No             | Rolling      |

---

# Common Commands

```bash id="commands"
docker stack deploy -c <file> <name>
docker stack ls
docker stack services <name>
docker stack ps <name>
docker stack rm <name>
docker service scale <service>=<replicas>
docker service logs <service>
```

---

# Troubleshooting

| Problem               | Cause             | Solution    |
| --------------------- | ----------------- | ----------- |
| Service not starting  | Image missing     | Build image |
| Swarm not initialized | Not in swarm mode | Run init    |
| Port conflict         | Port busy         | Change port |
| Pending replicas      | Resource issue    | Check nodes |

---

# Viva Questions

* Difference between Docker Compose and Stack?
* What is Docker Swarm?
* What is overlay network?
* How scaling works?
* What is service vs task?

---

# Conclusion

* Swarm cluster initialized
* Stack deployed successfully
* Services scaled
* Monitoring performed

Docker Stack provides production-ready orchestration with scaling, load balancing, and fault tolerance.

---

# References

* Docker Stack Docs
* Docker Swarm Docs
* Compose v3 Docs

---
