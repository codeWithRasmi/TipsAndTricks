## Run Kubernetes on Windows-WSL2
### Prerequisites

1. Enable WSL2 (Follow ).


2. Install a Linux distro (Skip this step if already done).

> wsl --install -d Ubuntu


3. Inside Ubuntu:

> sudo apt update && sudo apt upgrade -y


4. Install kubectl (Kubernetes CLI):
```
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```



---

### Install Minikube

Inside Ubuntu WSL:

> curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
> sudo install minikube-linux-amd64 /usr/local/bin/minikube

Check installation:

> minikube version


---

### Start Kubernetes Cluster

Run Minikube using Docker driver (works best inside WSL2):

> minikube start --driver=docker --memory=4096 --cpus=2

This creates a single-node Kubernetes cluster inside Docker.

Check status:

> kubectl cluster-info
> kubectl get nodes


---

### Deploy Your Java App

Suppose you already Dockerized your app as my-java-app:1.0.
If not, build inside WSL:

> docker build -t my-java-app:1.0 .

Load the image into Minikube:

> minikube image load my-java-app:1.0

Create a Kubernetes Deployment (deployment.yaml):

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-java-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-java-app
  template:
    metadata:
      labels:
        app: my-java-app
    spec:
      containers:
      - name: my-java-app
        image: my-java-app:1.0
        ports:
        - containerPort: 8080
```
Apply it:
```
kubectl apply -f deployment.yaml
kubectl get pods
```

---

### Expose with LoadBalancer (Production-like)

Create a service.yaml:
```
apiVersion: v1
kind: Service
metadata:
  name: my-java-service
spec:
  type: LoadBalancer
  selector:
    app: my-java-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```
Apply:

> kubectl apply -f service.yaml
> kubectl get svc

Since Minikube runs locally, you can tunnel the LoadBalancer:

> minikube tunnel

Now, your app is accessible at http://localhost 🎉


---

### Scale Up & Down

Increase replicas to 5:

> kubectl scale deployment my-java-app --replicas=5
> kubectl get pods

Scale down to 1:

> kubectl scale deployment my-java-app --replicas=1


---

### Ingress (Optional, for Production-like Routing)

Enable Ingress in Minikube:

> minikube addons enable ingress

Then create an Ingress resource to map domains like http://myapp.local/ → your service.


---
