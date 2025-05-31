# GazelHub

Great! Since your application is a **Node.js app using Vite and possibly React**, let’s walk through how to:

1. ✅ Build the app with **Docker**
2. ✅ Create a **Docker image**
3. ✅ Run it as a **container**

---

## 🚀 Step-by-Step: Dockerize a Node.js (Vite) Application

---

### 📁 Step 1: Confirm Project Structure

Your project should look something like this:

```
trackonomy-pilot-ai/
├── public/
├── src/
├── node_modules/
├── package.json
├── package-lock.json
├── vite.config.js
├── tsconfig.json (if using TypeScript)
```

---

### 📄 Step 2: Create a `Dockerfile`

In your project root, create a file named **`Dockerfile`**:

```Dockerfile
# Stage 1: Build the application
FROM node:20 AS builder

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# Stage 2: Serve the built app with a static server
FROM node:20

WORKDIR /app

# Install a lightweight static server
RUN npm install -g serve

# Copy build output from the previous stage
COPY --from=builder /app/dist ./dist

EXPOSE 3000

CMD ["serve", "-s", "dist", "-l", "3000"]
```

---

### 📄 Optional: `.dockerignore`

Create a `.dockerignore` file to speed up your build:

```dockerignore
node_modules
dist
.git
Dockerfile
*.log
```

---

### 🏗️ Step 3: Build the Docker Image

From your project directory:

```bash
docker build -t rondustech/trackonomy-frontend .
```

---

### 🧪 Step 4: Run the Container

```bash
docker run -p 3000:3000 rondustech/trackonomy-frontend
```

Then visit:

```
http://localhost:3000
```

or if you're on EC2:

```
http://<your-ec2-public-ip>:3000
```

> ✅ Make sure port 3000 is open in your EC2 security group

---

## 📤 Step 5: (Optional) Push to Docker Hub

```bash
docker tag trackonomy-frontend rondustech/trackonomy-frontend
docker push rondustech/trackonomy-frontend
```

---

## 🧼 Step 6: Clean Up (Optional)

```bash
docker container prune -f
docker image prune -f
```

---

## 🧠 Summary

| Task               | Command                         |
| ------------------ | ------------------------------- |
| Build image        | `docker build -t myapp .`       |
| Run container      | `docker run -p 3000:3000 myapp` |
| Push to Docker Hub | `docker push username/myapp`    |

---

Excellent! Let’s deploy your **Node.js (Vite) frontend app** to Kubernetes using your Docker image.

---

## 🎯 Goal:

Deploy `rondustech/trackonomy-frontend` Docker image to:

1. ✅ Minikube (local)
2. ✅ Amazon EKS (cloud)

---

# 🧱 Step-by-Step: Kubernetes Deployment Guide

---

## 🧾 Kubernetes YAML Manifests

Create a folder `k8s/` and add these files:

---

### 📄 `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: trackonomy-frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: trackonomy-frontend
  template:
    metadata:
      labels:
        app: trackonomy-frontend
    spec:
      containers:
      - name: frontend
        image: rondustech/trackonomy-frontend:latest
        ports:
        - containerPort: 3000
```

---

### 📄 `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: trackonomy-frontend-service
spec:
  type: LoadBalancer
  selector:
    app: trackonomy-frontend
  ports:
    - port: 80
      targetPort: 3000
      protocol: TCP
```

---

# 🚀 Option A: Deploy to **Minikube**

---

### ✅ Step 1: Start Minikube

```bash
minikube start
```

---

### ✅ Step 2: Apply the YAML Files

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

---

### ✅ Step 3: Access the App

```bash
minikube service trackonomy-frontend-service
```

> This opens a browser or gives you a local IP and port (e.g., `http://192.168.49.2:xxxx`)

---

# ☁️ Option B: Deploy to **Amazon EKS**

---

### ✅ Step 1: Set up EKS (if not done already)

```bash
eksctl create cluster --name trackonomy-eks --region us-west-2 --nodes 2
```

Then:

```bash
aws eks update-kubeconfig --name trackonomy-eks --region us-west-2
kubectl get nodes
```

---

### ✅ Step 2: Apply Your App YAMLs

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

---

### ✅ Step 3: Get LoadBalancer IP

```bash
kubectl get svc trackonomy-frontend-service
```

You’ll see something like:

```
NAME                         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)
trackonomy-frontend-service LoadBalancer   10.0.12.34     3.101.200.88     80:31234/TCP
```

Access the app in your browser:

```
http://<EXTERNAL-IP>
```

---

## 🧼 Optional: Cleanup

```bash
kubectl delete -f k8s/
```

Or delete the entire EKS cluster:

```bash
eksctl delete cluster --name trackonomy-eks --region us-west-2
```

---

## ✅ Bonus: Ready-to-Go GitHub Repo?

Would you like me to:

* ✅ Generate a GitHub repo template with this setup?
* ✅ Add CI/CD using GitHub Actions to auto-deploy to Minikube or EKS?
* ✅ Add Ingress, TLS, or DNS config?

Let me know what you want next!

