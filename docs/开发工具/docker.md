# Docker 使用指南

Docker 是一个开源的容器化平台，可以让开发者打包应用及其依赖到一个可移植的容器中。

## 安装 Docker

### Windows

1. 下载 Docker Desktop for Windows
2. 运行安装程序
3. 启动 Docker Desktop

### Linux

```bash
# Ubuntu
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 启动 Docker
sudo systemctl start docker

# 设置开机自启
sudo systemctl enable docker
```

## 基本概念

- **镜像（Image）**：只读的模板，用于创建容器
- **容器（Container）**：镜像的运行实例
- **仓库（Repository）**：存储镜像的地方

## 常用命令

### 镜像操作

```bash
# 搜索镜像
docker search ubuntu

# 拉取镜像
docker pull ubuntu:20.04

# 查看本地镜像
docker images

# 删除镜像
docker rmi ubuntu:20.04

# 构建镜像
docker build -t myapp:1.0 .

# 导出镜像
docker save -o ubuntu.tar ubuntu:20.04

# 导入镜像
docker load -i ubuntu.tar
```

### 容器操作

```bash
# 运行容器
docker run -it ubuntu:20.04 /bin/bash

# 后台运行容器
docker run -d --name mycontainer ubuntu:20.04

# 查看运行中的容器
docker ps

# 查看所有容器
docker ps -a

# 停止容器
docker stop mycontainer

# 启动容器
docker start mycontainer

# 重启容器
docker restart mycontainer

# 删除容器
docker rm mycontainer

# 进入运行中的容器
docker exec -it mycontainer /bin/bash

# 查看容器日志
docker logs mycontainer

# 查看容器详细信息
docker inspect mycontainer
```

## Dockerfile

### 基本结构

```dockerfile
# 基础镜像
FROM python:3.9-slim

# 设置工作目录
WORKDIR /app

# 复制文件
COPY requirements.txt .

# 安装依赖
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码
COPY . .

# 暴露端口
EXPOSE 8000

# 设置环境变量
ENV PYTHONUNBUFFERED=1

# 运行命令
CMD ["python", "app.py"]
```

### 常用指令

- `FROM`：指定基础镜像
- `WORKDIR`：设置工作目录
- `COPY`：复制文件到镜像
- `ADD`：复制文件（支持URL和自动解压）
- `RUN`：执行命令
- `CMD`：容器启动时执行的命令
- `ENTRYPOINT`：配置容器启动时的可执行文件
- `ENV`：设置环境变量
- `EXPOSE`：声明端口
- `VOLUME`：创建挂载点

## Docker Compose

### docker-compose.yml 示例

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/app
    environment:
      - DEBUG=1
    depends_on:
      - db
  
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Compose 命令

```bash
# 启动服务
docker-compose up

# 后台启动
docker-compose up -d

# 停止服务
docker-compose down

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs

# 重启服务
docker-compose restart

# 构建镜像
docker-compose build
```

## 数据管理

### 数据卷（Volume）

```bash
# 创建数据卷
docker volume create myvolume

# 查看数据卷
docker volume ls

# 使用数据卷
docker run -v myvolume:/data ubuntu:20.04

# 删除数据卷
docker volume rm myvolume
```

### 绑定挂载（Bind Mount）

```bash
# 挂载本地目录
docker run -v /host/path:/container/path ubuntu:20.04

# Windows 路径
docker run -v C:\Users\username\data:/data ubuntu:20.04
```

## 网络

### 网络类型

- **bridge**：默认网络模式
- **host**：使用主机网络
- **none**：无网络
- **container**：共享其他容器的网络

### 网络命令

```bash
# 创建网络
docker network create mynetwork

# 查看网络
docker network ls

# 连接容器到网络
docker network connect mynetwork mycontainer

# 断开网络连接
docker network disconnect mynetwork mycontainer

# 删除网络
docker network rm mynetwork
```

## 最佳实践

1. **使用官方镜像**作为基础镜像
2. **最小化层数**，合并 RUN 命令
3. **使用 .dockerignore** 排除不需要的文件
4. **不要在镜像中存储敏感信息**
5. **使用多阶段构建**减小镜像大小
6. **为镜像打标签**便于版本管理
7. **定期更新基础镜像**

## 多阶段构建示例

```dockerfile
# 构建阶段
FROM node:14 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 运行阶段
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## 常见问题

### 容器无法访问

检查端口映射和防火墙设置

### 容器启动后立即退出

查看日志：`docker logs container_name`

### 磁盘空间不足

清理未使用的资源：
```bash
docker system prune -a
```

## 应用场景

- 应用部署
- 微服务架构
- CI/CD 流程
- 开发环境统一
- 应用隔离
- 快速扩展
