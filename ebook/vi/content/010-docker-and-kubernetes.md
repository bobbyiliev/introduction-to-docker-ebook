# Chương 10: Docker trong Production: Orchestration với Kubernetes

Kubernetes (K8s) là một nền tảng container orchestration mã nguồn mở (open-source) giúp tự động hóa việc deployment, scaling và quản lý các ứng dụng container hóa (containerized applications). Nó hoạt động rất tốt với Docker và cung cấp một tập hợp các tính năng mạnh mẽ để chạy các containers trong môi trường production.

Kubernetes là một chủ đề riêng biệt rất rộng lớn, nhưng dưới đây là một số khái niệm chính và các quy tắc tốt nhất (best practices) khi sử dụng Kubernetes với Docker trong các môi trường production.

## Các khái niệm chính trong Kubernetes

1. **Pods**: Các đơn vị nhỏ nhất có thể triển khai (deployable units) trong Kubernetes, chứa một hoặc nhiều containers.
2. **Services**: Một cách trừu tượng để expose một ứng dụng đang chạy trên một nhóm các Pods.
3. **Deployments**: Mô tả trạng thái mong muốn cho các Pods và ReplicaSets.
4. **Namespaces**: Các cụm ảo (virtual clusters) bên trong một cụm vật lý (physical cluster).

## Thiết lập một Kubernetes Cluster

Bạn có thể thiết lập một Kubernetes cluster tại local bằng Minikube:

```bash
minikube start
```

Đối với môi trường production, hãy cân nhắc các dịch vụ Kubernetes được quản lý (managed Kubernetes services) như Google Kubernetes Engine (GKE), Amazon EKS, hoặc Azure AKS.

## Triển khai một Docker Container lên Kubernetes

1. Tạo một file Deployment YAML (`deployment.yaml`):

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

2. Áp dụng (Apply) Deployment:

```bash
kubectl apply -f deployment.yaml
```

3. Tạo một Service để expose Deployment (`service.yaml`):

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

4. Áp dụng Service:

```bash
kubectl apply -f service.yaml
```

## Scaling trong Kubernetes

Mở rộng deployment của bạn một cách dễ dàng:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

## Rolling Updates

Cập nhật ứng dụng của bạn mà không gây gián đoạn (no downtime):

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```

## Giám sát và Ghi nhật ký (Monitoring and Logging)

1. Xem logs của Pod:

```bash
kubectl logs <pod-name>
```

2. Sử dụng Prometheus và Grafana để giám sát:

```yaml
helm install prometheus stable/prometheus
helm install grafana stable/grafana
```

## Kubernetes Dashboard

Kích hoạt Kubernetes Dashboard cho giao diện đồ họa (GUI):

```bash
minikube addons enable dashboard
minikube dashboard
```

## Lưu trữ bền vững trong Kubernetes (Persistent Storage)

Sử dụng Persistent Volumes (PV) và Persistent Volume Claims (PVC):

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

1. **ClusterIP**: Expose Service trên một địa chỉ IP nội bộ của cluster.
2. **NodePort**: Expose Service trên IP của mỗi Node tại một cổng tĩnh (static port).
3. **LoadBalancer**: Expose Service ra bên ngoài sử dụng hệ thống cân bằng tải (load balancer) của nhà cung cấp đám mây.

## Kubernetes Secrets

Quản lý thông tin nhạy cảm:

```bash
kubectl create secret generic my-secret --from-literal=password=mysecretpassword
```

Sử dụng trong một Pod:

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

## Helm: Trình quản lý gói cho Kubernetes (The Kubernetes Package Manager)

Helm đơn giản hóa việc triển khai các ứng dụng phức tạp:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-release bitnami/wordpress
```

## Các quy tắc tốt nhất cho Kubernetes trong Production

1. Sử dụng namespaces để tổ chức các tài nguyên.
2. Thiết lập các yêu cầu (requests) và giới hạn (limits) tài nguyên.
3. Sử dụng liveness and readiness probes.
4. Triển khai hệ thống ghi nhật ký (logging) và giám sát (monitoring) phù hợp.
5. Thường xuyên cập nhật Kubernetes và ứng dụng của bạn.
6. Sử dụng Network Policies để kiểm soát mạng chi tiết.
7. Triển khai hệ thống RBAC (Role-Based Access Control) phù hợp.

## Kết luận

Kubernetes cung cấp một nền tảng mạnh mẽ để vận hành (orchestrating) các Docker containers trong môi trường production. Nó cung cấp các tính năng mạnh mẽ cho việc scaling, cập nhật và quản lý các ứng dụng container hóa. Mặc dù có một quá trình học hỏi nhất định, nhưng lợi ích của việc sử dụng Kubernetes cho việc triển khai Docker trong production là rất lớn, đặc biệt đối với các ứng dụng lớn và phức tạp.
