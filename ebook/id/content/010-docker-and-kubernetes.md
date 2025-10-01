# Bab 10: Docker di Produksi: Orkestrasi dengan Kubernetes

Kubernetes (K8s) adalah platform orkestrasi container open-source yang mengotomatisasi deployment, scaling, dan manajemen aplikasi tercontainerisasi. Kubernetes bekerja sangat baik dengan Docker dan menyediakan fitur lengkap untuk menjalankan container di lingkungan produksi.

Kubernetes adalah topik tersendiri, namun berikut adalah beberapa konsep kunci dan praktik terbaik untuk menggunakan Kubernetes bersama Docker di lingkungan produksi.

## Konsep Utama Kubernetes

1. **Pod**: Unit terkecil yang dapat dideploy di Kubernetes, berisi satu atau lebih container.
2. **Service**: Cara abstrak untuk mengekspos aplikasi yang berjalan di satu set Pod.
3. **Deployment**: Mendefinisikan state yang diinginkan untuk Pod dan ReplicaSet.
4. **Namespace**: Cluster virtual di dalam cluster fisik.

## Menyiapkan Cluster Kubernetes

Anda dapat menyiapkan cluster Kubernetes lokal menggunakan Minikube:

```bash
minikube start
```

Untuk produksi, pertimbangkan layanan Kubernetes terkelola seperti Google Kubernetes Engine (GKE), Amazon EKS, atau Azure AKS.

## Deploy Docker Container ke Kubernetes

1. Buat file Deployment YAML (`deployment.yaml`):

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

2. Terapkan Deployment:

```bash
kubectl apply -f deployment.yaml
```

3. Buat Service untuk mengekspos Deployment (`service.yaml`):

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

4. Terapkan Service:

```bash
kubectl apply -f service.yaml
```

## Scaling di Kubernetes

Skalakan deployment Anda dengan mudah:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

## Rolling Update

Update aplikasi Anda tanpa downtime:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```

## Monitoring dan Logging

1. Melihat log Pod:

```bash
kubectl logs <pod-name>
```

2. Gunakan Prometheus dan Grafana untuk monitoring:

```yaml
helm install prometheus stable/prometheus
helm install grafana stable/grafana
```

## Kubernetes Dashboard

Aktifkan Kubernetes Dashboard untuk GUI:

```bash
minikube addons enable dashboard
minikube dashboard
```

## Penyimpanan Persisten di Kubernetes

Gunakan Persistent Volume (PV) dan Persistent Volume Claim (PVC):

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

## Jaringan Kubernetes

1. **ClusterIP**: Mengekspos Service pada IP internal cluster.
2. **NodePort**: Mengekspos Service pada IP setiap Node dengan port statis.
3. **LoadBalancer**: Mengekspos Service secara eksternal menggunakan load balancer cloud provider.

## Kubernetes Secrets

Kelola informasi sensitif:

```bash
kubectl create secret generic my-secret --from-literal=password=mysecretpassword
```

Gunakan di Pod:

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

## Helm: Package Manager untuk Kubernetes

Helm memudahkan deployment aplikasi kompleks:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/wordpress
```

## Praktik Terbaik Kubernetes di Produksi

1. Gunakan namespace untuk mengorganisasi resource.
2. Terapkan resource requests dan limits.
3. Gunakan liveness dan readiness probe.
4. Implementasikan logging dan monitoring yang baik.
5. Rutin update Kubernetes dan aplikasi Anda.
6. Gunakan Network Policy untuk kontrol jaringan yang lebih detail.
7. Terapkan RBAC (Role-Based Access Control) dengan benar.

## Kesimpulan

Kubernetes menyediakan platform yang kuat untuk mengorkestrasi container Docker di lingkungan produksi. Kubernetes menawarkan fitur lengkap untuk scaling, update, dan manajemen aplikasi tercontainerisasi. Walaupun ada kurva pembelajaran, manfaat menggunakan Kubernetes untuk deployment Docker di produksi sangat besar, terutama untuk aplikasi besar dan kompleks.
