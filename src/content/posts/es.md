---
title: Elasticsearch Linux 服务器安装使用指南
description: 本指南详解在Linux服务器上安装与配置Elasticsearch的全过程，涵盖系统准备、三种安装方式、配置优化、常见问题解决等核心内容。
published: 2026-04-03
tags: [Elasticsearch, Linux, 搜索引擎, ELK Stack, 运维部署]
category: Linux
lang: zh-CN
image: /images/elasticsearch/cover.png
---

Elasticsearch是基于Apache Lucene的开源分布式搜索和分析引擎，提供RESTful API进行交互，支持近实时搜索和全文检索。

## 1. 环境要求

### 1.1 硬件配置

| 组件 | 最低配置 | 推荐配置 |
|------|----------|----------|
| CPU | 2核 | 4核以上 |
| 内存 | 4GB | 16GB以上 |
| 磁盘 | 10GB | 100GB以上 SSD |

### 1.2 软件要求

- **操作系统**：CentOS 7/8、Ubuntu 18.04/20.04/22.04、RHEL 7/8
- **Java**：JDK 11或更高版本（Elasticsearch 8.x自带OpenJDK）

### 1.3 系统参数要求

- 最大文件描述符：至少65535
- 最大线程数：至少4096
- 虚拟内存：至少262144

## 2. 安装前准备

### 2.1 创建Elasticsearch用户

```bash
sudo groupadd elasticsearch
sudo useradd -g elasticsearch -s /bin/bash -m elasticsearch
```

### 2.2 配置系统参数

**文件描述符限制**：

```bash
sudo vim /etc/security/limits.conf
# 添加以下内容
elasticsearch soft nofile 65535
elasticsearch hard nofile 65535
elasticsearch soft nproc 4096
elasticsearch hard nproc 4096
```

**虚拟内存设置**：

```bash
sudo vim /etc/sysctl.conf
vm.max_map_count=262144
sudo sysctl -p
```

**禁用Swap（推荐）**：

```bash
sudo swapoff -a
```

## 3. 安装Elasticsearch

### 3.1 RPM包安装（CentOS/RHEL）

```bash
# 导入GPG密钥
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# 创建仓库（清华大学镜像源）
sudo vim /etc/yum.repos.d/elasticsearch.repo
# 添加以下内容：
# [elasticsearch]
# baseurl=https://mirrors.tuna.tsinghua.edu.cn/elasticstack/yum/elastic-8.x/
# gpgcheck=1
# gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch

sudo yum install elasticsearch -y
```

### 3.2 DEB包安装（Ubuntu/Debian）

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://mirrors.tuna.tsinghua.edu.cn/elasticstack/apt/8.x stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install elasticsearch -y
```

### 3.3 tar.gz包安装（通用）

```bash
cd /opt
sudo wget https://mirrors.tuna.tsinghua.edu.cn/elasticstack/downloads/elasticsearch/elasticsearch-8.11.0-linux-x86_64.tar.gz
sudo tar -xzf elasticsearch-8.11.0-linux-x86_64.tar.gz
sudo mv elasticsearch-8.11.0 elasticsearch
sudo chown -R elasticsearch:elasticsearch /opt/elasticsearch
```

## 4. 启动服务

### 4.1 Systemd管理（RPM/DEB安装）

```bash
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
```

### 4.2 手动启动（tar.gz安装）

```bash
su - elasticsearch
/opt/elasticsearch/bin/elasticsearch -d -p pid
```

### 4.3 保存初始密码

安装完成后会显示自动生成的密码，请务必保存：

```
用户名：elastic
密码：d8Yoo5g5g*TFbcAFpJSg
```

重置密码命令：

```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

## 5. 配置Elasticsearch

### 5.1 配置文件位置

- **RPM/DEB安装**：`/etc/elasticsearch/elasticsearch.yml`
- **tar.gz安装**：`/opt/elasticsearch/config/elasticsearch.yml`

### 5.2 基本配置

```bash
sudo vim /etc/elasticsearch/elasticsearch.yml
```

添加以下内容：

```yaml
cluster.name: my-elasticsearch-cluster
node.name: node-1
discovery.type: single-node
network.host: 0.0.0.0
http.port: 9200
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

### 5.3 JVM配置

编辑`jvm.options`文件：

```bash
sudo vim /etc/elasticsearch/jvm.options
```

设置堆内存（建议不超过物理内存的50%）：

```
-Xms512m
-Xmx1g
```

### 5.4 2核2G环境优化配置

```yaml
indices.queries.cache.size: 5%
indices.requests.cache.size: 1%
thread_pool.write.queue_size: 200
thread_pool.search.queue_size: 500
```

### 5.5 目录权限

```bash
sudo chown -R elasticsearch:elasticsearch /var/lib/elasticsearch
sudo chown -R elasticsearch:elasticsearch /var/log/elasticsearch
sudo chown -R elasticsearch:elasticsearch /etc/elasticsearch
```

## 6. 验证安装

### 6.1 检查服务状态

```bash
# 等待服务启动（通常需要10-30秒）
sleep 15

# 使用curl测试
curl -k -u elastic:你的密码 https://localhost:9200
```

### 6.2 预期输出

```json
{
  "name" : "node-1",
  "cluster_name" : "my-elasticsearch-cluster",
  "version" : {
    "number" : "8.11.0"
  },
  "tagline" : "You Know, for Search"
}
```

### 6.3 查看集群健康

```bash
curl -k -u elastic:你的密码 https://localhost:9200/_cluster/health?pretty
```

健康状态说明：

- **green**：所有分片都可用
- **yellow**：主分片可用，副本分片不可用
- **red**：部分主分片不可用

## 7. 基本使用

### 7.1 索引操作

**创建索引**：

```bash
curl -k -X PUT "https://localhost:9200/products" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d @"{
    \"settings\": {
      \"number_of_shards\": 1,
      \"number_of_replicas\": 0
    },
    \"mappings\": {
      \"properties\": {
        \"name\": { \"type\": \"text\" },
        \"price\": { \"type\": \"float\" },
        \"description\": { \"type\": \"text\" },
        \"created_at\": { \"type\": \"date\" }
      }
    }
  }"@
```

**查看所有索引**：

```bash
curl -k -u elastic:你的密码 "https://localhost:9200/_cat/indices?v"
```

**删除索引**：

```bash
curl -k -X DELETE "https://localhost:9200/products" -u elastic:你的密码
```

### 7.2 文档操作

**添加文档**：

```bash
curl -k -X POST "https://localhost:9200/products/_doc" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"name\": \"笔记本电脑\", \"price\": 5999.00, \"description\": \"高性能办公笔记本\"}"

curl -k -X PUT "https://localhost:9200/products/_doc/1" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"name\": \"台式电脑\", \"price\": 8999.00}"
```

**查询文档**：

```bash
curl -k -X GET "https://localhost:9200/products/_doc/1?pretty" -u elastic:你的密码
curl -k -X GET "https://localhost:9200/products/_search?pretty" -u elastic:你的密码
```

**更新文档**：

```bash
curl -k -X POST "https://localhost:9200/products/_update/1" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"doc\": {\"price\": 7999.00}}"
```

**删除文档**：

```bash
curl -k -X DELETE "https://localhost:9200/products/_doc/1" -u elastic:你的密码
```

### 7.3 搜索查询

**全文搜索**：

```bash
curl -k -X GET "https://localhost:9200/products/_search?pretty" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"query\": {\"match\": {\"name\": \"电脑\"}}}"
```

**精确匹配**：

```bash
curl -k -X GET "https://localhost:9200/products/_search?pretty" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"query\": {\"term\": {\"price\": 5999.00}}}"
```

**范围查询**：

```bash
curl -k -X GET "https://localhost:9200/products/_search?pretty" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"query\": {\"range\": {\"price\": {\"gte\": 100, \"lte\": 6000}}}}"
```

**组合查询**：

```bash
curl -k -X GET "https://localhost:9200/products/_search?pretty" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"query\": {\"bool\": {\"must\": [{\"match\": {\"description\": \"办公\"}}], \"filter\": [{\"range\": {\"price\": {\"lte\": 6000}}}]}}}"
```

## 8. 安全配置

### 8.1 重置用户密码

```bash
cd /usr/share/elasticsearch/bin
sudo ./elasticsearch-reset-password -u elastic
```

### 8.2 创建新用户

```bash
curl -k -X POST "https://localhost:9200/_security/user/myuser" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"password\": \"mypassword\", \"roles\": [\"kibana_admin\", \"monitoring_user\"]}"
```

### 8.3 配置防火墙

```bash
# CentOS/RHEL
sudo firewall-cmd --permanent --add-port=9200/tcp
sudo firewall-cmd --permanent --add-port=9300/tcp
sudo firewall-cmd --reload

# Ubuntu/Debian
sudo ufw allow 9200/tcp
sudo ufw allow 9300/tcp
```

## 9. 常见问题与解决方案

### 9.1 vm.max_map_count is too low

```bash
sudo sysctl -w vm.max_map_count=262144
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
```

### 9.2 max file descriptors too low

编辑`/etc/security/limits.conf`添加：

```
elasticsearch soft nofile 65535
elasticsearch hard nofile 65535
```

然后重新登录elasticsearch用户。

### 9.3 OutOfMemoryError

```bash
sudo vim /etc/elasticsearch/jvm.options
-Xms512m
-Xmx1g
sudo systemctl restart elasticsearch
```

### 9.4 集群状态为Yellow

单节点模式下设置副本数为0：

```bash
curl -k -X PUT "https://localhost:9200/_settings" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"index\": {\"number_of_replicas\": 0}}"
```

### 9.5 无法连接Elasticsearch

```bash
sudo systemctl status elasticsearch
sudo netstat -tlnp | grep 9200
sudo tail -f /var/log/elasticsearch/*.log
```

## 10. 备份与恢复

### 10.1 创建快照仓库

```bash
sudo mkdir -p /opt/elasticsearch/backup
sudo chown -R elasticsearch:elasticsearch /opt/elasticsearch/backup

# 编辑elasticsearch.yml添加：path.repo: ["/opt/elasticsearch/backup"]

curl -k -X PUT "https://localhost:9200/_snapshot/my_backup" \
  -u elastic:你的密码 \
  -H "Content-Type: application/json" \
  -d "{\"type\": \"fs\", \"settings\": {\"location\": \"/opt/elasticsearch/backup\"}}"
```

### 10.2 创建快照

```bash
curl -k -X PUT "https://localhost:9200/_snapshot/my_backup/snapshot_1?wait_for_completion=true" \
  -u elastic:你的密码
```

### 10.3 恢复快照

```bash
curl -k -X POST "https://localhost:9200/_snapshot/my_backup/snapshot_1/_restore" \
  -u elastic:你的密码
```

## 11. 监控与维护

```bash
# 集群健康
curl -k -u elastic:你的密码 "https://localhost:9200/_cluster/health?pretty"

# 节点信息
curl -k -u elastic:你的密码 "https://localhost:9200/_cat/nodes?v"

# 索引状态
curl -k -u elastic:你的密码 "https://localhost:9200/_cat/indices?v&s=store.size:desc"

# 强制合并索引
curl -k -X POST "https://localhost:9200/products/_forcemerge?max_num_segments=1" \
  -u elastic:你的密码

# 清理缓存
curl -k -X POST "https://localhost:9200/_cache/clear" -u elastic:你的密码
```

## 12. 重要文件路径

| 类型 | RPM/DEB路径 | tar.gz路径 |
|------|-------------|------------|
| 配置文件 | /etc/elasticsearch | /opt/elasticsearch/config |
| 数据目录 | /var/lib/elasticsearch | /opt/elasticsearch/data |
| 日志目录 | /var/log/elasticsearch | /opt/elasticsearch/logs |

## 13. 常用端口

| 端口 | 用途 |
|------|------|
| 9200 | HTTP REST API |
| 9300 | 节点间通信 |

## 结语

本文详细介绍了Elasticsearch在Linux服务器上的完整安装和使用流程。建议：
1. 合理配置集群（生产环境至少3个主节点）
2. 定期备份重要数据
3. 监控集群健康状态和性能指标
4. 根据业务需求调整索引策略
5. 保持软件版本更新
