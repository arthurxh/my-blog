---
title: docker.md
published: 2026-07-02
description: ''
image: ''
tags: []
category: ''
draft: false 
lang: ''
---
### docker原理
### 1. 什么是docker

Docker是一种容器化平台，它将应用程序及其依赖项打包成轻量级、可移植的容器。与虚拟机不同，容器共享宿主机内核，启动速度快、资源占用低。Docker通过镜像实现应用的一致性交付，让开发、测试、生产环境保持一致，解决了"在我机器上能运行"的难题，大幅提升了部署效率和环境一致性。

### 2. 镜像与容器的类比

#### 📦 镜像（Image）= 蛋糕模具

镜像就像是一个蛋糕模具——它包含了制作蛋糕所需的所有配方（代码、运行环境、依赖库、配置文件等），是一个静态的、只读的模板。一次制作，多次使用，不会自己"动"，只是一个蓝图，体积小、可复制、可分享。

#### 🥧 容器（Container）= 烤好的蛋糕

容器是基于镜像"烤"出来的实际蛋糕——它是镜像的运行时实例，拥有独立的文件系统、进程空间和网络配置。可以启动、停止、暂停、删除，运行时状态是动态变化的，多个容器可以同时运行，互不干扰。

#### ⚖️ 核心区别

| 特性 | 镜像 (Image) | 容器 (Container) |
|------|-------------|-----------------|
| **状态** | 静态、只读 | 动态、可读写 |
| **作用** | 定义"做什么" | 实际"运行什么" |
| **生命周期** | 持久保存 | 随用随创建/销毁 |
| **类比** | 蛋糕模具 | 烤好的蛋糕 |

#### 🎯 一句话总结

镜像就是"说明书"，容器就是"按说明书生产出来的产品"。一个镜像可以启动成百上千个容器，就像一个模具可以烤出无数个蛋糕一样！

### 3. Dockerfile与多阶段构建

Dockerfile是构建镜像的脚本文件，通过一系列指令（FROM、RUN、COPY、CMD等）定义镜像的构建过程。多阶段构建是Docker 17.05引入的特性，允许在一个Dockerfile中使用多个FROM指令，每个阶段可以使用不同的基础镜像。

**优势：**
- **减小镜像体积**：只保留最终运行所需的文件，丢弃构建过程中的临时文件
- **提高安全性**：构建环境和运行环境分离，避免将敏感信息带入生产镜像

**示例：**
```dockerfile
# 构建阶段
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# 运行阶段
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 4. Docker Compose编排

Docker Compose是用于定义和运行多容器Docker应用的工具。通过一个YAML文件（docker-compose.yml）配置应用的服务、网络、卷等，使用一条命令即可启动整个应用栈。

**核心概念：**
- **Service**：定义一个容器服务
- **Network**：容器间的网络通信
- **Volume**：数据持久化存储

**示例（docker-compose.yml）：**
```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "80:80"
    depends_on:
      - db
  db:
    image: mysql:8
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
volumes:
  mysql-data:
```

### 5. 数据卷与持久化

容器的文件系统是临时的，容器删除后数据会丢失。数据卷（Volume）是Docker提供的持久化存储方案。

**三种数据持久化方式：**

| 方式 | 特点 | 适用场景 |
|------|------|----------|
| **Named Volume** | Docker管理，存储在宿主机特定目录 | 数据库、配置文件 |
| **Bind Mount** | 挂载宿主机任意目录 | 开发时代码热更新 |
| **tmpfs** | 内存中的临时文件系统 | 敏感数据、临时缓存 |

**数据卷优势：**
- 数据独立于容器生命周期
- 便于容器间共享数据
- 支持备份、恢复和迁移

### 6. Docker网络模式

Docker提供多种网络模式，满足不同场景的网络需求：

| 模式 | 说明 |
|------|------|
| **bridge** | 默认模式，容器间通过虚拟网桥通信 |
| **host** | 共享宿主机网络栈，性能最高 |
| **none** | 完全隔离，无网络连接 |
| **container** | 共享另一个容器的网络命名空间 |
| **自定义网络** | 用户创建的隔离网络，推荐用于多容器通信 |

**最佳实践：**
- 使用自定义网络隔离不同应用
- 通过服务名进行容器间通信
- 避免使用host网络模式
