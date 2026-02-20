# Lab 2: Docker Installation, Configuration, and Running Images

---

## 1. Objective

The objectives of this experiment are:

- To pull Docker images from Docker Hub
- To run containers using Docker
- To manage container lifecycle (start, stop, remove)
- To remove Docker images

---

## 2. Procedure

---

## Step 1: Pull Docker Image

The nginx image was pulled from Docker Hub using the following command:

```bash
docker pull nginx
```

![Docker Pull](../screenshots/lab2/Screenshot%20(519).png)

**Observation:**  
Docker successfully downloaded the nginx image layers from Docker Hub and stored them locally.

---

## Step 2: Run Container with Port Mapping

A container was created and run in detached mode with port mapping.

```bash
docker run -d -p 8080:80 nginx
```
![Docker Pull](../screenshots/lab2/Screenshot%20(518).png)

![Docker Pull](../screenshots/lab2/Screenshot%20(516).png)

**Observation:**  
The container started successfully in detached mode. Port 8080 on the host machine was mapped to port 80 inside the container.

---

## Step 3: Verify Running Containers

To verify the running containers, the following command was used:

```bash
docker ps
```

![Docker Pull](../screenshots/lab2/Screenshot%20(517).png)

**Observation:**  
The nginx container was listed as running with its container ID, image name, and mapped ports.

---

## Step 4: Stop and Remove Container

To stop the running container:

```bash
docker stop <container_id>
```

To remove the container:

```bash
docker rm <container_id>
```


**Observation:**  
The container was successfully stopped and removed from the system.

---

## Step 5: Remove Docker Image

To remove the nginx image:

```bash
docker rmi nginx
```

![Docker Pull](../screenshots/lab2/Screenshot%20(517).png)

**Observation:**  
The nginx image was removed from the local Docker image repository.

---

## 3. Result

Docker images were successfully pulled from Docker Hub.  
Containers were created, executed, verified, stopped, and removed.  
The container lifecycle management commands were performed successfully.