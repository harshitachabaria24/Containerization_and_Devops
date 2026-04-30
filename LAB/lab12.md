# Experiment 12

## Container Orchestration using Kubernetes

---

# Objective

To learn Kubernetes basics by deploying a WordPress application using Minikube, including:

* Deployments
* Services
* Scaling Pods
* Self-healing

---

# Tools & Environment

* Minikube
* kubectl
* Ubuntu 22.04
* Docker

---

# Theory

Kubernetes is an open-source container orchestration platform used to automate deployment, scaling, and management of containerized applications.

---

## Key Concepts

| Term       | Description                        |
| ---------- | ---------------------------------- |
| Pod        | Smallest unit (contains container) |
| Deployment | Manages replicas of pods           |
| Service    | Exposes pods                       |
| NodePort   | External access to service         |
| ReplicaSet | Maintains pod count                |
| kubectl    | CLI tool                           |

---

# Steps Performed

---

## Step 1: Create Deployment YAML

```bash id="nano"
nano wordpress-deployment.yaml
```

### wordpress-deployment.yaml

```yaml id="deployment"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress

spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress

  template:
    metadata:
      labels:
        app: wordpress

    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        ports:
        - containerPort: 80
```

📸 Screenshot:
`![Deployment YAML](../screenshots/lab12/Screenshot%20(725).png)

---

## Step 2: Start Minikube & Apply Deployment

```bash id="start"
minikube start
kubectl apply -f wordpress-deployment.yaml
```

📸 Screenshot:
`![Minikube Start](../screenshots/lab12/Screenshot%20(725).png)

---

## Step 3: Create Service YAML

```bash id="nano2"
nano wordpress-service.yaml
```

### wordpress-service.yaml

```yaml id="service"
apiVersion: v1
kind: Service

metadata:
  name: wordpress-service

spec:
  type: NodePort
  selector:
    app: wordpress

  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
```

---

## Step 4: Apply Service

```bash id="applysvc"
kubectl apply -f wordpress-service.yaml
```

📸 Screenshot:
`![Service Apply](../screenshots/lab12/Screenshot%20(726).png)

---

## Step 5: Verify Pods

```bash id="pods"
kubectl get pods
```

📸 Screenshot:
`![Pods](../screenshots/lab12/Screenshot%20(726).png)

---

## Step 6: Verify Service

```bash id="svc"
kubectl get svc
```

---

## Step 7: Scale Deployment

```bash id="scale"
kubectl scale deployment wordpress --replicas=4
```

📸 Screenshot:
`![Scaling](../screenshots/lab12/Screenshot%20(726).png)

---

## Step 8: Verify Scaled Pods

```bash id="pods2"
kubectl get pods
```

📸 Screenshot:
`![Scaled Pods](../screenshots/lab12/Screenshot%20(726).png)

---

## Step 9: Delete a Pod (Self-Healing Test)

```bash id="delete"
kubectl delete pod <pod-name>
```

---

## Step 10: Verify Self-Healing

```bash id="verify"
kubectl get pods
```

📸 Screenshot:
`![Self Healing](../screenshots/lab12/Screenshot%20(726).png)

---

# Commands Summary

| Command                    | Purpose       |
| -------------------------- | ------------- |
| minikube start             | Start cluster |
| kubectl apply -f file.yaml | Apply config  |
| kubectl get pods           | List pods     |
| kubectl get svc            | List services |
| kubectl scale deployment   | Scale pods    |
| kubectl delete pod         | Delete pod    |

---

# Observations

* Deployment created 2 pods initially
* Service exposed app on NodePort 30007
* Scaling created additional pods instantly
* Self-healing replaced deleted pod automatically

---

# Conclusion

* Kubernetes manages deployments efficiently
* Scaling is automatic and fast
* Self-healing ensures availability
* Infrastructure complexity is abstracted

---
