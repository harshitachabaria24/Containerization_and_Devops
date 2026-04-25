# Experiment 7

## CI/CD using Jenkins, GitHub and Docker Hub

---

### Name: Harshita Chabaria

### Subject: Containerization and DevOps

---

# Aim

To design and implement a complete CI/CD pipeline using Jenkins, integrating source code from GitHub, and building & pushing Docker images to Docker Hub.

---

# Objectives

* Understand CI/CD workflow using Jenkins (GUI-based tool)
* Create a structured GitHub repository with application and Jenkinsfile
* Build Docker images from source code
* Securely store Docker Hub credentials in Jenkins
* Automate build and push process using webhook triggers
* Use same host (Docker) as Jenkins agent

---

# Theory

## 1. What is Jenkins?

Jenkins is a web-based GUI automation server used to build, test, and deploy applications. It supports plugins (GitHub, Docker, etc.) and uses **Pipeline as Code (Jenkinsfile)**.

---

## 2. What is CI/CD?

* **Continuous Integration (CI):** Automatically builds and tests code after each commit
* **Continuous Deployment (CD):** Automatically delivers built artifacts (Docker images)

---

## 3. Workflow Overview

```id="workflow"
Developer → GitHub → Webhook → Jenkins → Build → Docker Hub
```

---

## 4. Prerequisites

* Docker & Docker Compose installed
* GitHub account
* Docker Hub account
* Basic Linux knowledge

---

# Part A: GitHub Repository Setup

## Project Structure

```id="structure"
My-App/
├── app.py
├── requirements.txt
├── Dockerfile
└── Jenkinsfile
```

---

## Application Code

### app.py

```python id="appcode"
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from CI/CD Pipeline!"

app.run(host="0.0.0.0", port=80)
```

---

### requirements.txt

```txt id="req"
flask
```

---

### Dockerfile

```dockerfile id="dockerfile"
FROM python:3.10-slim

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

EXPOSE 80

CMD ["python", "app.py"]
```

---

### Jenkinsfile

```groovy id="jenkinsfile"
pipeline {
    agent any

    environment {
        IMAGE_NAME = "har4049/myapp"
    }

    stages {

        stage('Clone Source') {
            steps {
                git branch: 'main', url: 'https://github.com/harshitachabaria24/My-App'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh 'echo $DOCKER_TOKEN | docker login -u jasman1201 --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}
```

---

# Part B: Jenkins Setup using Docker

## docker-compose.yml

```yaml id="compose"
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: always
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    user: root

volumes:
  jenkins_home:
```

---

## Steps

### Run Jenkins

```bash id="runjenkins"
docker compose up -d
```

📸 Screenshot:
`![Jenkins Started](../screenshots/lab7/Screenshot%20(691).png)

---

### Access Jenkins

http://localhost:8080
`![Jenkins Started](../screenshots/lab7/Screenshot%202026-04-25%20225057.png)
`![Console Output](../screenshots/lab7/Screenshot%20(698).png)

### Get Admin Password

```bash id="password"
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

---

### Install Plugins

* Click **Install Suggested Plugins**

---

### Install Docker inside Jenkins

```bash id="dockerinstall"
apt-get update && apt-get install -y docker.io
```

📸 Screenshot:
`![Docker Installed](../screenshots/lab7/Screenshot%20(693).png)

---

### Verify Docker

```bash id="dockerps"
docker ps
```

📸 Screenshot:
`![Docker PS](../screenshots/lab7/Screenshot%20(695).png)
`![Console Output](../screenshots/lab7/Screenshot%20(694).png)

---

# Part C: Jenkins Configuration

---

### Add Docker Hub Credentials

Path:
**Manage Jenkins → Credentials → Global → Add Credentials**

📸 Screenshot:
`![Credentials](../screenshots/lab7/Screenshot%20(688).png)

---

### Configure Pipeline Job

* New Item → Pipeline
* Pipeline script from SCM
* Git Repo URL
* Branch: main
* Script Path: Jenkinsfile

📸 Screenshot:
`![Pipeline Config](../screenshots/lab7/Screenshot%20(690).png)

---

### Pipeline Execution

📸 Screenshot:
`![Console Output](../screenshots/lab7/Screenshot%20(687).png)
`![Console Output](../screenshots/lab7/Screenshot%20(697).png)

---

## Execution Flow

| Stage              | Action                | Status    |
| ------------------ | --------------------- | --------- |
| Clone Source       | Pull code from GitHub | ✅ Success |
| Build Docker Image | Build image           | ✅ Success |
| Login Docker Hub   | Authenticate          | ✅ Success |
| Push Image         | Push to Docker Hub    | ✅ Success |

---

## Role of Same Host Agent

Jenkins runs inside Docker with socket mounted:

```id="socket"
/var/run/docker.sock → /var/run/docker.sock
```

➡️ This allows Jenkins to control host Docker directly

---

# Observations

* Jenkins GUI simplifies CI/CD
* GitHub stores code + pipeline
* Docker ensures consistency
* Credentials stored securely
* Docker socket enables direct control

---

# Result

Successfully implemented CI/CD pipeline where:

* Code + Jenkinsfile in GitHub
* Jenkins auto-builds on changes
* Docker image built
* Image pushed to Docker Hub securely

---
