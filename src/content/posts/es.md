---
title: Elasticsearch Linux 服务器安装使用指南
description: 详解在Linux上安装配置Elasticsearch的方法
published: 2026-04-03
tags: [Linux, Elasticsearch]
category: Linux
lang: zh-CN
image: /images/elasticsearch/cover.png
---

Elasticsearch是基于Lucene的分布式搜索引擎。

## 环境要求

CPU 2核以上, 内存 4GB以上

## 安装前准备

创建用户:
groupadd elasticsearch
useradd -g elasticsearch -m elasticsearch

配置参数:
echo elasticsearch soft nofile 65535 >> /etc/security/limits.conf
sysctl -w vm.max_map_count=262144

## 安装

RPM安装:
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
yum install elasticsearch -y

DEB安装:
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
echo deb https://mirrors.tuna.tsinghua.edu.cn/elasticstack/apt/8.x stable main > /etc/apt/sources.list.d/elastic-8.x.list
apt-get update && apt-get install elasticsearch -y

tar.gz安装:
cd /opt && wget https://mirrors.tuna.tsinghua.edu.cn/elasticstack/downloads/elasticsearch/elasticsearch-8.11.0-linux-x86_64.tar.gz && tar -xzf elasticsearch-8.11.0-linux-x86_64.tar.gz

## 启动
systemctl daemon-reload && systemctl enable elasticsearch && systemctl start elasticsearch

## 配置
编辑 /etc/elasticsearch/elasticsearch.yml:
cluster.name: my-cluster
node.name: node-1
discovery.type: single-node
network.host: 0.0.0.0
http.port: 9200

JVM堆内存设置 jvm.options:
-Xms512m
-Xmx1g

## 验证
curl -k -u elastic:密码 https://localhost:9200

## 基本操作

创建索引:
curl -k -X PUT https://localhost:9200/myindex -u elastic:密码

添加文档:
curl -k -X POST https://localhost:9200/myindex/_doc -u elastic:密码

## 常见问题
vm.max_map_count过低: sysctl -w vm.max_map_count=262144

## 端口
9200 HTTP API
9300 节点通信
