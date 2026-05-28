---
title: nerdctl
type: concept
tags: [docker, containerd, container, cli, kubernetes]
sources:
  - "[2026-05-26-nerdctl](raw/clippings/2026-05-26-nerdctl.md)"
---

# nerdctl

nerdctl 是一个 Docker 兼容的容器运行时 CLI 工具，由 Apache containerd 社区维护，提供与 Docker CLI 相似的用户体验，同时支持 containerd 的高级特性。

## 与 Docker 的核心区别

### 架构对比

| 组件 | Docker | nerdctl |
|------|--------|---------|
| **CLI** | `docker` | `nerdctl` |
| **守护进程** | `dockerd`（独立进程） | 直接调用 `containerd` |
| **容器运行时** | containerd → runc | runc |
| **K8s 兼容性** | 需 dockershim 桥接层 | 直接兼容，无需桥接 |

```
Docker 架构:
CLI → dockerd → containerd → runc → containers
             (dockershim bridge)

nerdctl 架构:
CLI → containerd → runc → containers
     (直接调用)
```

### 高级特性对比

| 特性 | Docker | nerdctl |
|------|--------|---------|
| **rootless 模式** | 受限，需额外配置 | ✅ 原生支持，无需 root |
| **镜像签名** | ❌ 不支持 | ✅ `nerdctl sign` |
| **镜像加密** | ❌ 不支持 | ✅ `nerdctl image encrypt/decrypt` |
| **Gfarm 全局去重** | ❌ 不支持 | ✅ 支持镜像层共享 |
| **构建缓存去重** | ❌ 不支持 | ✅ `--duplicate-ref` |
| **沙箱隔离** | 有限 | ✅ 原生 namespace isolation |
| **内存占用** | 较高（~100MB） | 较低（~30MB） |
| **启动速度** | 稍慢（多一层） | 更快（直接调用） |

## 使用场景

| 场景 | 推荐 | 原因 |
|------|------|------|
| **本地开发** | Docker | 生态完善，文档丰富，插件丰富 |
| **K8s 生产环境** | nerdctl | containerd 原生，减少代理层开销 |
| **rootless 容器** | nerdctl | 原生支持，无需 root 权限 |
| **镜像安全（签名/加密）** | nerdctl | Docker 不支持这些功能 |
| **资源受限环境** | nerdctl | 内存占用更小 |

## 常用命令

| 功能 | Docker | nerdctl |
|------|--------|---------|
| 运行容器 | `docker run -d nginx` | `nerdctl run -d nginx` |
| 构建镜像 | `docker build -t img .` | `nerdctl build -t img .` |
| 查看容器 | `docker ps` | `nerdctl ps` |
| 编排服务 | `docker compose up -d` | `nerdctl compose up -d` |
| 拉取镜像 | `docker pull nginx` | `nerdctl pull nginx` |
| **镜像加密** | — | `nerdctl image encrypt` |
| **镜像签名** | — | `nerdctl sign` |
| **rootless 运行** | — | `nerdctl --cgroup-manager=cgroupfs run` |

## 安装方式

```bash
# Linux/macOS (官方安装脚本)
curl -sSL https://raw.githubusercontent.com/containerd/nerdctl/main/install.sh | sh

# macOS (Homebrew)
brew install nerdctl

# 从 GitHub Releases 手动下载
# https://github.com/containerd/nerdctl/releases
```

## 发展历程

| 时间 | 版本 | 里程碑 |
|------|------|--------|
| 2019 | v0.1 | 首个正式版本发布 |
| 2020 | v0.5 | 添加 Compose 支持 |
| 2021 | v0.8 | 添加 rootless 模式支持 |
| 2022 | v1.0 | 生产可用，Docker 兼容完善 |
| 2023 | v1.4 | 添加镜像加密/签名高级功能 |
| 2024 | v2.0 | 性能优化，更好的 K8s 集成 |

## 相关

- [Docker Containerization_Docker容器化](Docker%20Containerization_Docker容器化.md)
- [Kubernetes_Kubernetes](Kubernetes_Kubernetes.md)
- [containerd](https://containerd.io/) — 容器运行时核心