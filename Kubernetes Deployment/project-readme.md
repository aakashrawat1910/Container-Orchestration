# Microservices Kubernetes Deployment

This project demonstrates deploying a microservices architecture on Kubernetes using Minikube. The application consists of four services: User, Product, Order, and Gateway services.

## Prerequisites

- Docker installed
- Minikube installed
- kubectl installed

## Minikube Setup Instructions

1. Start Minikube with adequate resources:

```bash
minikube start --driver=docker --cpus=2 --memory=4g
```

2. Enable the Ingress controller:

```bash
minikube addons enable ingress
```

3. Set your Docker environment to use Minikube's Docker daemon:

```bash
eval $(minikube docker-env)
```

## Building the Docker Images

If you need to build the Docker images locally:

```bash
# Navigate to each service directory and build the image
docker build -t your-registry/user-service:latest ./user-service
docker build -t your-registry/product-service:latest ./product-service
docker build -t your-registry/order-service:latest ./order-service
docker build -t your-registry/gateway-service:latest ./gateway-service
```

## Deployment Steps

1. Create the Kubernetes namespace (optional):

```bash
kubectl create namespace microservices
kubectl config set-context --current --namespace=microservices
```

2. Apply all deployment configurations:

```bash
kubectl apply -f deployments/
```

3. Apply all service configurations:

```bash
kubectl apply -f services/
```

4. Apply the Ingress configuration:

```bash
kubectl apply -f ingress/
```

5. Verify all resources are created correctly:

```bash
kubectl get deployments
kubectl get services
kubectl get pods
kubectl get ingress
```

## Testing Procedures

1. Add the hostname to your local hosts file:

```bash
echo "$(minikube ip) microservices.local" | sudo tee -a /etc/hosts
```

2. Access the services via the Ingress:

```bash
# User Service
curl http://microservices.local/api/users

# Product Service
curl http://microservices.local/api/products

# Order Service
curl http://microservices.local/api/orders

# Gateway Service (default route)
curl http://microservices.local/
```

3. Verify internal service communication:

```bash
# Get a pod name
kubectl get pods

# Execute a shell in the gateway pod
kubectl exec -it <gateway-pod-name> -- /bin/sh

# From inside the pod, test communication with other services
curl http://user-service:3000/users
curl http://product-service:3001/products
curl http://order-service:3002/orders
```

## Troubleshooting Guide

### Common Issues and Solutions

1. **Pods are not running:**
   - Check pod status: `kubectl get pods`
   - Check detailed pod information: `kubectl describe pod <pod-name>`
   - Check pod logs: `kubectl logs <pod-name>`

2. **Services are not accessible:**
   - Verify service endpoints: `kubectl get endpoints`
   - Test service inside the cluster: `kubectl run -i --tty --rm debug --image=busybox --restart=Never -- wget -O- <service-name>:<port>/`

3. **Ingress is not routing correctly:**
   - Check Ingress status: `kubectl describe ingress microservices-ingress`
   - Verify Ingress controller is running: `kubectl get pods -n kube-system`
   - Check Ingress controller logs: `kubectl logs -n kube-system <ingress-controller-pod-name>`

4. **DNS issues:**
   - Verify DNS resolution: `kubectl run -i --tty --rm debug --image=busybox --restart=Never -- nslookup <service-name>`

5. **Resource constraints:**
   - Check resource usage: `kubectl top pods`
   - Increase Minikube resources if needed: `minikube stop && minikube start --driver=docker --cpus=4 --memory=8g`

### If All Else Fails

Reset your Minikube cluster:

```bash
minikube delete
minikube start --driver=docker --cpus=2 --memory=4g
```

## Screenshots

### Running Pods
![Running Pods](screenshots/running-pods.png)

### Successful Ingress Access
![Successful Ingress Access](screenshots/ingress-access.png)

### Service Communication Tests
![Service Communication Tests](screenshots/service-communication.png)
