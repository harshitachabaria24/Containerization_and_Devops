# Lab 3: Deploying NGINX Using Different Base Images and Comparing Image Layers

---

## Lab Objectives

After completing this lab, students will be able to:

- Deploy NGINX using:
  - Official NGINX image
  - Ubuntu-based image
  - Alpine-based image
- Understand Docker image layers and size differences
- Compare performance, security, and use-cases
- Explain real-world use of NGINX in containerized systems

---

# Part 1: Deploy NGINX Using Official Image (Recommended Approach)

## Step 1: Pull the Image

```bash
docker pull nginx:latest
```
![Official Image Pull](../screenshots/lab3/Screenshot%20(533).png)
---

## Step 2: Run the Container

```bash
docker run -d --name nginx-official -p 8080:80 nginx
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(534).png)

---

## Step 3: Verify

```bash
curl http://localhost:8080
```

You should see the NGINX welcome page.

![Official Image Pull](../screenshots/lab3/Screenshot%20(532).png)

---

## Key Observations

```bash
docker images nginx
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(535).png)

- Image is pre-optimized
- Minimal configuration required
- Uses Debian-based OS internally

---

# Part 2: Custom NGINX Using Ubuntu Base Image

## Step 1: Create Dockerfile

Create a file named `Dockerfile` and add:

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(536).png)

---

## Step 2: Build Image

```bash
docker build -t nginx-ubuntu .
```



---

## Step 3: Run Container

```bash
docker run -d --name nginx-ubuntu -p 8081:80 nginx-ubuntu
```



---

## Observations

```bash
docker images nginx-ubuntu
```



- Much larger image size
- More layers
- Full OS utilities available

---

# Part 3: Custom NGINX Using Alpine Base Image

## Step 1: Create Dockerfile

```dockerfile
FROM alpine:latest

RUN apk add --no-cache nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```



---

## Step 2: Build Image

```bash
docker build -t nginx-alpine .
```



---

## Step 3: Run Container

```bash
docker run -d --name nginx-alpine -p 8082:80 nginx-alpine
```



---

## Observations

```bash
docker images nginx-alpine
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(537).png)

- Extremely small image
- Fewer packages
- Faster pull and startup time

---

# Part 4: Image Size and Layer Comparison

## Compare Sizes

```bash
docker images | grep nginx
```



### Typical Size Comparison

| Image Type        | Approx Size |
|-------------------|------------|
| nginx:latest     | ~140 MB |
| nginx-ubuntu     | ~220+ MB |
| nginx-alpine     | ~25-30 MB |

---

## Inspect Layers

```bash
docker history nginx
docker history nginx-ubuntu
docker history nginx-alpine
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(537).png)

### Observations

- Ubuntu image has many filesystem layers
- Alpine has minimal layers
- Official NGINX image is optimized but heavier than Alpine

---

# Part 5: Functional Task Using NGINX

## Task 1: Serve Custom HTML Page

```bash
mkdir html
echo "<h1>Hello from Docker NGINX</h1>" > html/index.html
```

Run container with volume mounting:

```bash
docker run -d \
-p 8083:80 \
-v $(pwd)/html:/usr/share/nginx/html \
nginx
```


---

## Result

NGINX was successfully deployed using three different base images.  
Image sizes, layers, and performance characteristics were compared.  
Custom HTML content was served using volume mounting.

---

# Experiment 3: Deploying Web Applications with Docker

In this experiment, we deploy a sample web application using Docker. This includes writing a custom Dockerfile, building a Docker image, and running the application inside a containerized environment.

This experiment demonstrates the complete lifecycle of containerizing and deploying a web application.

---

## Objectives

By completing this experiment, we will:

1. Prepare a simple web application for containerization.
2. Write a Dockerfile defining the application environment.
3. Build a custom Docker image.
4. Run the web app inside a Docker container.
5. Verify that the application works correctly in the container.

---

# Step 1: Choose a Sample Application

We selected a lightweight Python Flask web application.

### Sample Flask Application (app.py)

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```
![Official Image Pull](../screenshots/lab3/Screenshot%20(538).png)

![Official Image Pull](../screenshots/lab3/Screenshot%20(539).png)
---

# Step 2: Create Application Files

Inside the project directory, create:

- `app.py`
- `requirements.txt`

### requirements.txt

```
flask
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(540).png)

![Official Image Pull](../screenshots/lab3/Screenshot%20(541).png)
---

# Step 3: Test the Application Locally

Before containerizing, test locally:

```bash
python app.py
```

Visit:

```
http://localhost:8080
```

Expected Output:

```
Hello, Docker!
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(548).png)

Fix any errors before proceeding.

---

# Step 4: Writing the Dockerfile

Create a file named `Dockerfile`.

## Dockerfile Content

```dockerfile
# Base Image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port
EXPOSE 8080

# Start application
CMD ["python", "app.py"]
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(542).png)
![Official Image Pull](../screenshots/lab3/Screenshot%20(543).png)

---

## Explanation of Dockerfile Sections

### 1. Base Image

`FROM python:3.9-slim`

This provides a lightweight Python runtime environment.

---

### 2. Copying Files

`COPY . /app`

Copies all application files into the container.

---

### 3. Installing Dependencies

`RUN pip install --no-cache-dir -r requirements.txt`

Installs Flask and other dependencies.

---

### 4. Entrypoint / Command

`CMD ["python", "app.py"]`

Defines the default command to run the application.

---

# Step 5: Build the Docker Image

Run:

```bash
docker build -t flask-docker-app .
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(544).png)
![Official Image Pull](../screenshots/lab3/Screenshot%20(545).png)

---

# Step 6: Run the Docker Container

```bash
docker run -d -p 8080:8080 --name flask-container flask-docker-app
```

![Official Image Pull](../screenshots/lab3/Screenshot%20(546).png)
---

# Step 7: Verify the Application

Open in browser:

```
http://localhost:8080
```

Expected Output:

```
Hello, Docker!
```
![Official Image Pull](../screenshots/lab3/Screenshot%20(547).png)

---

# Troubleshooting (If Issues Occur)

If nothing loads:

- Check container status:

```bash
docker ps
```

- Check container logs:

```bash
docker logs flask-container
```



---

# Result

The web application was successfully containerized using Docker.  
A custom image was built, the container was launched, and the application was accessed via a web browser.

This experiment demonstrates the complete workflow of deploying a web application in a containerized environment.
