# EffiTime 部署指南

## 环境要求

### 硬件要求
- **CPU**: 2核及以上
- **内存**: 4GB及以上
- **存储**: 50GB及以上 SSD
- **网络**: 稳定的互联网连接

### 软件要求
- **操作系统**: Ubuntu 20.04 LTS 或 CentOS 8+
- **Docker**: 20.10+
- **Docker Compose**: 2.0+
- **Git**: 2.30+

## 快速部署

### 1. 安装Docker和Docker Compose

```bash
# 安装Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# 安装Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 验证安装
docker --version
docker-compose --version
```

### 2. 克隆项目

```bash
git clone https://github.com/aoqisun/effitime.git
cd effitime
```

### 3. 配置环境变量

```bash
cd deploy
cp env.production.example .env
```

编辑 `.env` 文件，更新以下关键配置：

```bash
# 数据库配置
MYSQL_ROOT_PASSWORD=your_secure_password_here
MYSQL_PASSWORD=your_db_password_here

# JWT配置  
JWT_SECRET=your_256_bit_secret_key_here

# 域名配置
CORS_ORIGIN=https://yourdomain.com
DOMAIN=yourdomain.com
API_DOMAIN=api.yourdomain.com

# Redis配置
REDIS_PASSWORD=your_redis_password_here
```

### 4. SSL证书配置

#### 使用Let's Encrypt（推荐）

```bash
# 安装Certbot
sudo apt update
sudo apt install certbot

# 获取SSL证书
sudo certbot certonly --standalone -d yourdomain.com -d api.yourdomain.com

# 复制证书到项目目录
sudo cp /etc/letsencrypt/live/yourdomain.com/fullchain.pem deploy/nginx/ssl/cert.pem
sudo cp /etc/letsencrypt/live/yourdomain.com/privkey.pem deploy/nginx/ssl/key.pem
```

#### 自签名证书（仅测试）

```bash
mkdir -p deploy/nginx/ssl
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout deploy/nginx/ssl/key.pem \
  -out deploy/nginx/ssl/cert.pem
```

### 5. 启动服务

```bash
cd deploy
docker-compose up -d
```

### 6. 验证部署

```bash
# 检查服务状态
docker-compose ps

# 检查日志
docker-compose logs -f

# 健康检查
curl https://yourdomain.com/health
```

## 详细配置

### Nginx配置

编辑 `deploy/nginx/nginx.conf`：

```nginx
server {
    listen 80;
    server_name yourdomain.com api.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.yourdomain.com;
    
    ssl_certificate /etc/nginx/ssl/cert.pem;
    ssl_certificate_key /etc/nginx/ssl/key.pem;
    
    location / {
        proxy_pass http://backend:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 数据库初始化

创建 `deploy/sql/init.sql`：

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS effitime_prod CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 使用数据库
USE effitime_prod;

-- 导入表结构和初始数据
SOURCE /docker-entrypoint-initdb.d/schema.sql;
```

### 备份策略

创建备份脚本 `scripts/backup.sh`：

```bash
#!/bin/bash

BACKUP_DIR="/opt/effitime/backups"
DATE=$(date +"%Y%m%d_%H%M%S")

# 数据库备份
docker exec effitime-mysql mysqldump -u root -p$MYSQL_ROOT_PASSWORD effitime_prod > $BACKUP_DIR/db_$DATE.sql

# 文件备份
tar -czf $BACKUP_DIR/uploads_$DATE.tar.gz /opt/effitime/uploads

# 清理旧备份（7天前）
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete
```

设置定时任务：

```bash
# 编辑crontab
crontab -e

# 添加每日凌晨2点备份
0 2 * * * /opt/effitime/scripts/backup.sh
```

## 监控和维护

### 日志管理

```bash
# 查看实时日志
docker-compose logs -f backend

# 查看特定服务日志
docker-compose logs mysql
docker-compose logs redis
docker-compose logs nginx
```

### 性能监控

```bash
# 系统资源使用
docker stats

# 数据库性能
docker exec effitime-mysql mysql -u root -p$MYSQL_ROOT_PASSWORD -e "SHOW PROCESSLIST;"

# Redis状态
docker exec effitime-redis redis-cli info
```

### 常用维护命令

```bash
# 重启服务
docker-compose restart backend

# 更新代码
git pull origin main
docker-compose pull
docker-compose up -d

# 清理无用镜像
docker system prune -f

# 查看容器状态
docker-compose ps
```

## 故障排除

### 常见问题

**1. 数据库连接失败**
```bash
# 检查MySQL容器状态
docker logs effitime-mysql

# 重启数据库
docker-compose restart mysql
```

**2. 后端服务无响应**
```bash
# 检查后端日志
docker logs effitime-backend

# 检查端口占用
sudo netstat -tlnp | grep :3000
```

**3. SSL证书问题**
```bash
# 检查证书有效性
openssl x509 -in deploy/nginx/ssl/cert.pem -text -noout

# 更新Let's Encrypt证书
sudo certbot renew
```

### 紧急恢复

```bash
# 停止所有服务
docker-compose down

# 恢复数据库备份
docker exec -i effitime-mysql mysql -u root -p$MYSQL_ROOT_PASSWORD effitime_prod < backup.sql

# 重新启动
docker-compose up -d
```

## 安全配置

### 防火墙设置

```bash
# 允许HTTP/HTTPS流量
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 22/tcp

# 禁止直接访问数据库端口
sudo ufw deny 3306/tcp
sudo ufw deny 6379/tcp

# 启用防火墙
sudo ufw enable
```

### 定期安全更新

```bash
# 系统更新
sudo apt update && sudo apt upgrade -y

# Docker镜像更新
docker-compose pull
docker-compose up -d
```

---

**文档维护**: DevOps团队  
**最后更新**: 2025-01-27