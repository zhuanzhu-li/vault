---
title: Docker Containerization（Docker容器化）
type: concept
tags: [docker, containers, devops, virtualization, cloud-native]
sources: []
---

# Docker Containerization（Docker容器化）

Docker 是一个开源的容器化平台，通过容器技术实现应用的轻量化打包、分发和运行，是现代云原生应用的基础设施。

## 核心概念

### 容器 vs 虚拟机

| 维度 | 容器 | 虚拟机 |
|------|------|--------|
| **启动速度** | 秒级 | 分钟级 |
| **资源占用** | MB 级 | GB 级 |
| **隔离性** | 进程级隔离 | 硬件级隔离 |
| **性能** | 接近原生 | 有虚拟化损耗 |
| **可移植性** | 跨平台 | 受镜像限制 |

### 核心组件

| 组件 | 说明 |
|------|------|
| **Image** | 只读模板，包含应用和依赖 |
| **Container** | 镜像的运行实例 |
| **Registry** | 镜像仓库（Docker Hub、私有仓库） |
| **Dockerfile** | 构建镜像的脚本 |

## Dockerfile 最佳实践

### 基础镜像选择

```dockerfile
# 优先使用官方轻量镜像
FROM eclipse-temurin:17-jre-alpine

# 多阶段构建，减小镜像体积
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/myapp.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 优化技巧

| 技巧 | 说明 |
|------|------|
| **利用缓存** | COPY/ADD 依赖文件在前，代码在后 |
| **清理缓存** | RUN 删除 apt/yum 缓存 |
| **使用 .dockerignore** | 排除不需要的文件 |
| **最小化层数** | 合并 RUN 指令 |
| **非 root 用户** | 提高安全性 |

### .dockerignore

```
.git
.target
*.md
*.class
.gitignore
```

## 常用命令

### 镜像操作

```bash
# 构建镜像
docker build -t myapp:1.0 .

# 查看镜像
docker images

# 拉取镜像
docker pull openjdk:17-slim

# 删除镜像
docker rmi myapp:1.0

# 推送镜像
docker push registry.example.com/myapp:1.0
```

### 容器操作

```bash
# 运行容器
docker run -d --name myapp -p 8080:8080 \
  -e SPRING_PROFILES_ACTIVE=prod \
  -v /data/logs:/app/logs \
  myapp:1.0

# 查看运行中的容器
docker ps

# 查看所有容器（包括停止的）
docker ps -a

# 停止/启动容器
docker stop myapp
docker start myapp

# 进入容器
docker exec -it myapp /bin/sh

# 查看日志
docker logs -f myapp

# 删除容器
docker rm myapp
```

### 资源限制

```bash
# 限制 CPU 和内存
docker run -d --name myapp \
  --cpus="1.5" \
  --memory="512m" \
  --memory-swap="1g" \
  myapp:1.0
```

## Docker Compose

### 编排多容器应用

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    networks:
      - backend

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - backend

volumes:
  pgdata:

networks:
  backend:
    driver: bridge
```

### 常用命令

```bash
# 启动所有服务
docker-compose up -d

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs -f app

# 停止所有服务
docker-compose down

# 重新构建
docker-compose up -d --build
```

## 网络模式

| 模式 | 说明 | 使用场景 |
|------|------|---------|
| **bridge** | 默认模式，容器在桥接网络中 | 开发环境 |
| **host** | 容器共享主机网络 | 性能敏感场景 |
| **overlay** | 跨主机容器通信 | Docker Swarm |
| **none** | 无网络 | 离线任务 |

### 容器间通信

```bash
# 通过服务名通信（在同一网络中）
# app 容器可以 ping db 访问数据库
docker network create mynet
docker run --network mynet --name app myapp:1.0
docker run --network mynet --name db postgres:15
# app 中连接 postgres://db:5432
```

## 数据持久化

### Volume 类型

| 类型 | 说明 |
|------|------|
| **匿名卷** | `docker run -v /data` 由 Docker 管理 |
| **命名卷** | `docker run -v mydata:/data` 跨容器复用 |
| **绑定挂载** | `docker run -v /host/path:/container/path` 主机目录 |

### 数据备份恢复

```bash
# 备份
docker run --rm -v mydata:/data -v $(pwd):/backup alpine \
  tar czf /backup/backup.tar.gz /data

# 恢复
docker run --rm -v mydata:/data -v $(pwd):/backup alpine \
  tar xzf /backup/backup.tar.gz -C /
```

## 安全最佳实践

| 实践 | 说明 |
|------|------|
| **最小化权限** | 使用非 root 用户运行应用 |
| **扫描漏洞** | 定期使用 Trivy/Clair 扫描镜像 |
| **只读文件系统** | `--read-only` 防止写入 |
| **资源限制** | 防止容器耗尽系统资源 |
| ** Secrets 管理** | 敏感信息用 Docker Secrets |

```dockerfile
# 使用非 root 用户
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

## 相关

- [Kubernetes_Kubernetes](Kubernetes_Kubernetes.md)
- [CI/CD Practices_CI/CD实践](CI%20CD%20Practices_CI%20CD实践.md)
