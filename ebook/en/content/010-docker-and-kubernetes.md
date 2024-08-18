# Chapter 10: Docker in Production: Orchestration with Kubernetes

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It works well with Docker and provides a robust set of features for running containers in production.

Kubernetes is a topic of its own, but here are some key concepts and best practices for using Kubernetes with Docker in production environments.

## Key Kubernetes Concepts

1. **Pods**: The smallest deployable units in Kubernetes, containing one or more containers.
2. **Services**: An abstract way to expose an application running on a set of Pods.
3. **Deployments**: Describe the desired state for Pods and ReplicaSets.
4. **Namespaces**: Virtual clusters within a physical cluster.

## Setting Up a Kubernetes Cluster

You can set up a local Kubernetes cluster using Minikube:

```bash
minikube start
```

For production, consider managed Kubernetes services like Google Kubernetes Engine (GKE), Amazon EKS, or Azure AKS.

## Deploying a Docker Container to Kubernetes

1. Create a Deployment YAML file (`deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

2. Apply the Deployment:

```bash
kubectl apply -f deployment.yaml
```

3. Create a Service to expose the Deployment (`service.yaml`):

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

4. Apply the Service:

```bash
kubectl apply -f service.yaml
```

## Scaling in Kubernetes

Scale your deployment easily:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

## Rolling Updates

Update your application without downtime:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```

## Monitoring and Logging

1. View Pod logs:

```bash
kubectl logs <pod-name>
```

2. Use Prometheus and Grafana for monitoring:

```yaml
helm install prometheus stable/prometheus
helm install grafana stable/grafana
```

## Kubernetes Dashboard

Enable the Kubernetes Dashboard for a GUI:

```bash
minikube addons enable dashboard
minikube dashboard
```

## Persistent Storage in Kubernetes

Use Persistent Volumes (PV) and Persistent Volume Claims (PVC):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

## Kubernetes Networking

1. **ClusterIP**: Exposes the Service on a cluster-internal IP.
2. **NodePort**: Exposes the Service on each Node's IP at a static port.
3. **LoadBalancer**: Exposes the Service externally using a cloud provider's load balancer.

## Kubernetes Secrets

Manage sensitive information:

```bash
kubectl create secret generic my-secret --from-literal=password=mysecretpassword
```

Use in a Pod:

```yaml
spec:
  containers:
  - name: myapp
    image: myapp
    env:
    - name: SECRET_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```

## Helm: The Kubernetes Package Manager

Helm simplifies deploying complex applications:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/wordpress
```

## Best Practices for Kubernetes in Production

1. Use namespaces to organize resources.
2. Implement resource requests and limits.
3. Use liveness and readiness probes.
4. Implement proper logging and monitoring.
5. Regularly update Kubernetes and your applications.
6. Use Network Policies for fine-grained network control.
7. Implement proper RBAC (Role-Based Access Control).

## Conclusion

Kubernetes provides a powerful platform for orchestrating Docker containers in production environments. It offers robust features for scaling, updating, and managing containerized applications. While there's a learning curve, the benefits of using Kubernetes for production Docker deployments are significant, especially for large, complex applications.
