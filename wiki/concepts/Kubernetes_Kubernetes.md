---
title: Kubernetes（Kubernetes）
type: concept
tags: [kubernetes, k8s, containers, orchestration, cloud-native]
sources: []
---

# Kubernetes（Kubernetes）

Kubernetes（简称 K8s）是一个开源的容器编排平台，用于自动化容器化应用的部署、扩缩容和管理，是云原生时代的核心基础设施。

## 核心概念

### 架构图

```
┌─────────────────────────────────────────────────────────────┐
│                      Kubernetes 集群                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐         ┌─────────────┐                  │
│  │   Master    │         │   Worker    │                  │
│  │  ┌────────┐ │         │  ┌────────┐ │                  │
│  │  │API Server│ │         │  │  Kubelet │ │                  │
│  │  ├────────┤ │         │  ├────────┤ │                  │
│  │  │Scheduler │ │         │  │Kube-Proxy│ │                  │
│  │  ├────────┤ │         │  └────────┘ │                  │
│  │  │Controller│ │         │  ┌────────┐ │                  │
│  │  ├────────┤ │         │  │ Container │ │                  │
│  │  │ etcd   │ │         │  │ Runtime  │ │                  │
│  │  └────────┘ │         │  └────────┘ │                  │
│  └─────────────┘         └─────────────┘                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Master 组件

| 组件 | 说明 |
|------|------|
| **API Server** | 集群统一入口，REST API |
| **Scheduler** | 调度 Pod 到合适的 Node |
| **Controller Manager** | 维护集群状态，控制器 |
| **etcd** | 分布式键值存储，保存集群状态 |

### Node 组件

| 组件 | 说明 |
|------|------|
| **Kubelet** | 容器运行时管理，维护 Pod |
| **Kube-Proxy** | 网络代理，维护网络规则 |
| **Container Runtime** | Docker/containerd 运行容器 |

## 核心资源对象

### Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
  labels:
    app: my-app
    version: v1
spec:
  containers:
  - name: my-app
    image: myapp:1.0
    ports:
    - containerPort: 8080
    resources:
      requests:
        memory: "128Mi"
        cpu: "250m"
      limits:
        memory: "256Mi"
        cpu: "500m"
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 5
```

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: myapp:1.0
        ports:
        - containerPort: 8080
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-svc
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP  # ClusterIP / NodePort / LoadBalancer
```

### HPA（自动扩缩容）

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

## 网络模型

### Service 类型

| 类型 | 说明 | 使用场景 |
|------|------|---------|
| **ClusterIP** | 集群内部访问 | 内部服务间调用 |
| **NodePort** | 通过节点端口访问 | 开发测试 |
| **LoadBalancer** | 云厂商负载均衡 | 生产环境 |
| **ExternalName** | DNS 别名 | 外部服务映射 |

### 网络通信

```
Pod → Service → Endpoints → Pod
      (DNS 解析)
```

## 存储卷

### 类型

| 类型 | 说明 |
|------|------|
| **emptyDir** | 临时存储，随 Pod 删除 |
| **hostPath** | 挂载主机目录 |
| **PersistentVolumeClaim** | 持久化存储 |
| **ConfigMap** | 配置文件 |
| **Secret** | 敏感信息 |

### PVC 示例

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

## 资源配额

### ResourceQuota

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: my-quota
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    pods: "100"
```

### LimitRange

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: my-limits
spec:
  limits:
  - type: Container
    default:
      cpu: 500m
      memory: 256Mi
    defaultRequest:
      cpu: 100m
      memory: 128Mi
```

## 常用命令

```bash
# 部署应用
kubectl apply -f deployment.yaml

# 查看 Pod
kubectl get pods
kubectl get pods -o wide

# 查看日志
kubectl logs -f my-app-pod-name

# 进入容器
kubectl exec -it my-app-pod-name -- /bin/sh

# 扩缩容
kubectl scale deployment my-app --replicas=5

# 查看资源使用
kubectl top pods
kubectl top nodes

# 端口转发
kubectl port-forward my-app-pod-name 8080:8080

# 滚动更新
kubectl set image deployment/my-app my-app=myapp:2.0
kubectl rollout status deployment/my-app

# 回滚
kubectl rollout undo deployment/my-app
```

## 调度策略

| 策略 | 说明 |
|------|------|
| **NodeSelector** | 标签选择器 |
| **NodeAffinity** | 节点亲和性 |
| **PodAffinity** | Pod 亲和性/反亲和性 |
| **Taints/Tolerations** | 污点和容忍 |
| **PriorityClass** | Pod 优先级 |

## 相关

- [Docker Containerization_Docker容器化](Docker%20Containerization_Docker容器化.md)
- [CI/CD Practices_CI/CD实践](CI%20CD%20Practices_CI%20CD实践.md)
