---
title: CI/CD Practices（CI/CD实践）
type: concept
tags: [devops, cicd, jenkins, gitlab-ci, deployment]
sources: []
---

# CI/CD Practices（CI/CD实践）

CI/CD（持续集成/持续交付）是现代软件工程的核心实践，通过自动化构建、测试和部署流程，提升软件交付效率和质量。

## CI/CD 流程

```
┌─────────────────────────────────────────────────────────────┐
│                     CI/CD 流程                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌────────┐   ┌────────┐   ┌────────┐   ┌────────┐     │
│  │  代码   │──▶│  构建   │──▶│  测试   │──▶│  部署   │     │
│  │  提交   │   │  编译   │   │  单元   │   │  预发   │     │
│  │         │   │  打包   │   │  集成   │   │  生产   │     │
│  └────────┘   └────────┘   └────────┘   └────────┘     │
│       │                                                  │
│       │  Merge Request / Pull Request                    │
│       ▼                                                  │
│  ┌────────┐   ┌────────┐   ┌────────┐                  │
│  │ Code   │──▶│  安全   │──▶│  镜像   │                  │
│  │ Review │   │  扫描   │   │  构建   │                  │
│  └────────┘   └────────┘   └────────┘                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## GitLab CI

### .gitlab-ci.yml 示例

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_IMAGE: registry.example.com/myapp
  KUBECONFIG: /tmp/kubeconfig

build:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean package -DskipTests
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 hour

test:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn test
  coverage: '/Total.*?([0-9]{1,3})%/'

sonar-check:
  stage: test
  image: sonarsource/sonar-scanner-cli
  script:
    - sonar-scanner -Dsonar.projectKey=myapp
  allow_failure: true  # 允许失败，不阻塞流水线

build-image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_IMAGE:$CI_COMMIT_SHA .
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD registry.example.com
    - docker push $DOCKER_IMAGE:$CI_COMMIT_SHA
  only:
    - main

deploy-staging:
  stage: deploy
  script:
    - kubectl apply -f k8s/ deployment.yaml
    - kubectl set image deployment/myapp myapp=$DOCKER_IMAGE:$CI_COMMIT_SHA
  environment:
    name: staging
  only:
    - main

deploy-production:
  stage: deploy
  script:
    - kubectl apply -f k8s/ deployment.yaml
    - kubectl set image deployment/myapp myapp=$DOCKER_IMAGE:$CI_COMMIT_SHA
  environment:
    name: production
    url: https://myapp.example.com
  when: manual  # 手动触发
  only:
    - main
```

## Jenkins Pipeline

### Jenkinsfile 示例

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'myapp'
        REGISTRY = 'registry.example.com'
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                echo 'Scanning for vulnerabilities...'
                sh 'trivy image --exit-code 1 --severity HIGH,CRITICAL $REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh """
                    docker build -t $REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER .
                    docker push $REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER
                """
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging...'
                sh """
                    kubectl config use-context staging
                    kubectl set image deployment/myapp myapp=$REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER
                """
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh """
                    kubectl config use-context production
                    kubectl set image deployment/myapp myapp=$REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER
                """
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        failure {
            notifySlack('#alerts', 'Pipeline failed')
        }
    }
}
```

## 部署策略

### 蓝绿部署

```
┌─────────────────────────────────────────────┐
│                   负载均衡                   │
│                   ┌──────┐                  │
│                   │ 80%  │──▶ Blue (v1)     │
│                   │ 20%  │──▶ Green (v2)   │
│                   └──────┘                  │
└─────────────────────────────────────────────┘
```

### 金丝雀发布

```
┌─────────────────────────────────────────────┐
│                   负载均衡                   │
│                   ┌──────┐                  │
│                   │ 95%  │──▶ Old (v1)     │
│                   │  5%  │──▶ Canary (v2)  │
│                   └──────┘                  │
└─────────────────────────────────────────────┘
```

### 滚动更新

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # 最多超出期望副本数
      maxUnavailable: 0   # 最多不可用副本数
```

## 容器镜像最佳实践

| 实践 | 说明 |
|------|------|
| **版本标签** | 使用 SHA 而非 latest |
| **多阶段构建** | 减小镜像体积 |
| **最小化基础镜像** | alpine/scratch |
| **非 root 用户** | 提高安全性 |
| **健康检查** | Liveness/Readiness Probe |
| **资源限制** | CPU/内存限制 |

## 质量 gates

| 检查项 | 工具 |
|------|------|
| **代码风格** | Checkstyle, ESLint |
| **单元测试** | JUnit, pytest |
| **代码覆盖率** | JaCoCo, Coverage.py |
| **代码复杂度** | SonarQube, CodeClimate |
| **安全扫描** | Trivy, Snyk, OWASP |
| **依赖扫描** | OWASP Dependency-Check |

## 相关

- [Docker Containerization_Docker容器化](Docker%20Containerization_Docker容器化.md)
- [Kubernetes_Kubernetes](Kubernetes_Kubernetes.md)
