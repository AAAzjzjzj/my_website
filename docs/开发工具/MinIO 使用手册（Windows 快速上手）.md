# MinIO 使用手册（Windows 快速上手）

MinIO 是一个高性能的对象存储服务，兼容 Amazon S3 API。

## 安装

### Windows 安装

1. 下载 MinIO 服务器

```powershell
# 下载 MinIO
Invoke-WebRequest -Uri "https://dl.min.io/server/minio/release/windows-amd64/minio.exe" -OutFile "C:\minio\minio.exe"
```

2. 创建数据目录

```powershell
mkdir C:\minio\data
```

3. 启动 MinIO

```powershell
# 设置访问密钥
$env:MINIO_ROOT_USER="admin"
$env:MINIO_ROOT_PASSWORD="password123"

# 启动服务器
C:\minio\minio.exe server C:\minio\data --console-address ":9001"
```

4. 访问控制台

- API 端点：http://localhost:9000
- 控制台：http://localhost:9001
- 用户名：admin
- 密码：password123

## 基本概念

- **Bucket**：存储桶，类似文件夹
- **Object**：对象，存储的文件
- **Access Key**：访问密钥
- **Secret Key**：密钥

## 使用 MinIO Client (mc)

### 安装 mc

```powershell
# 下载 mc
Invoke-WebRequest -Uri "https://dl.min.io/client/mc/release/windows-amd64/mc.exe" -OutFile "C:\minio\mc.exe"
```

### 配置别名

```powershell
# 添加 MinIO 服务器
C:\minio\mc.exe alias set myminio http://localhost:9000 admin password123

# 查看别名
C:\minio\mc.exe alias list
```

### 基本操作

```powershell
# 创建 bucket
mc mb myminio/mybucket

# 列出 buckets
mc ls myminio

# 上传文件
mc cp file.txt myminio/mybucket/

# 下载文件
mc cp myminio/mybucket/file.txt ./

# 删除文件
mc rm myminio/mybucket/file.txt

# 删除 bucket
mc rb myminio/mybucket

# 同步目录
mc mirror local-dir/ myminio/mybucket/
```

## Python SDK

### 安装

```bash
pip install minio
```

### 基本使用

```python
from minio import Minio
from minio.error import S3Error

# 创建客户端
client = Minio(
    "localhost:9000",
    access_key="admin",
    secret_key="password123",
    secure=False  # HTTP
)

# 创建 bucket
bucket_name = "mybucket"
if not client.bucket_exists(bucket_name):
    client.make_bucket(bucket_name)
    print(f"Bucket '{bucket_name}' created")

# 上传文件
client.fput_object(
    bucket_name,
    "myfile.txt",
    "local_file.txt"
)
print("File uploaded")

# 下载文件
client.fget_object(
    bucket_name,
    "myfile.txt",
    "downloaded_file.txt"
)
print("File downloaded")

# 列出对象
objects = client.list_objects(bucket_name)
for obj in objects:
    print(obj.object_name)

# 删除对象
client.remove_object(bucket_name, "myfile.txt")
print("File deleted")

# 删除 bucket
client.remove_bucket(bucket_name)
print("Bucket deleted")
```

### 上传大文件

```python
# 分片上传
client.fput_object(
    "mybucket",
    "large_file.zip",
    "local_large_file.zip",
    part_size=10*1024*1024  # 10MB 分片
)
```

### 生成预签名 URL

```python
from datetime import timedelta

# 生成下载链接（有效期 7 天）
url = client.presigned_get_object(
    "mybucket",
    "myfile.txt",
    expires=timedelta(days=7)
)
print(f"Download URL: {url}")

# 生成上传链接
url = client.presigned_put_object(
    "mybucket",
    "upload_file.txt",
    expires=timedelta(hours=1)
)
print(f"Upload URL: {url}")
```

## 设置 Bucket 策略

### 公开读取

```python
import json

# 设置 bucket 为公开读取
policy = {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {"AWS": "*"},
            "Action": ["s3:GetObject"],
            "Resource": [f"arn:aws:s3:::mybucket/*"]
        }
    ]
}

client.set_bucket_policy("mybucket", json.dumps(policy))
```

### 使用 mc 设置策略

```powershell
# 设置为公开下载
mc anonymous set download myminio/mybucket

# 设置为公开上传
mc anonymous set upload myminio/mybucket

# 设置为公开读写
mc anonymous set public myminio/mybucket

# 取消公开访问
mc anonymous set none myminio/mybucket
```

## 事件通知

### 配置 Webhook

```python
from minio import Minio
from minio.notificationconfig import (
    NotificationConfig, QueueConfig, PrefixFilterRule
)

# 配置 webhook
config = NotificationConfig(
    queue_config_list=[
        QueueConfig(
            "arn:minio:sqs::primary:webhook",
            ["s3:ObjectCreated:*"],
            config_id="1",
            prefix_filter_rule=PrefixFilterRule("images/")
        )
    ]
)

client.set_bucket_notification("mybucket", config)
```

## Docker 部署

### 单节点部署

```bash
docker run -p 9000:9000 -p 9001:9001 \
  --name minio \
  -e "MINIO_ROOT_USER=admin" \
  -e "MINIO_ROOT_PASSWORD=password123" \
  -v D:\minio\data:/data \
  minio/minio server /data --console-address ":9001"
```

### Docker Compose

```yaml
version: '3.8'

services:
  minio:
    image: minio/minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: password123
    volumes:
      - minio_data:/data
    command: server /data --console-address ":9001"

volumes:
  minio_data:
```

## 最佳实践

1. **使用强密码**：设置复杂的访问密钥
2. **启用 HTTPS**：生产环境使用 SSL/TLS
3. **定期备份**：备份重要数据
4. **设置生命周期策略**：自动清理过期文件
5. **监控存储使用**：定期检查存储空间
6. **使用版本控制**：启用对象版本控制

## 生命周期管理

```python
from minio.lifecycleconfig import (
    LifecycleConfig, Rule, Expiration
)
from datetime import timedelta

# 设置 30 天后过期
config = LifecycleConfig(
    [
        Rule(
            rule_id="expire-30-days",
            status="Enabled",
            expiration=Expiration(days=30)
        )
    ]
)

client.set_bucket_lifecycle("mybucket", config)
```

## 常见问题

### 连接被拒绝

检查防火墙设置，确保端口 9000 和 9001 开放

### 权限错误

确认访问密钥和密钥正确

### 上传失败

检查磁盘空间和文件权限

## 应用场景

- 图片/视频存储
- 备份和归档
- 大数据存储
- 日志存储
- 静态网站托管
- 机器学习数据集存储
