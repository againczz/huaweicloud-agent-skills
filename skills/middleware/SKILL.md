---
name: huaweicloud-middleware
description: 华为云中间件服务 (DMS/CSS)。当用户提到分布式消息服务、DMS、Kafka、RabbitMQ、Topic、消费组、云搜索服务、CSS、Elasticsearch、ES集群、搜索引擎时触发。
---

# 华为云中间件服务

通过 KooCLI (`hcloud`) 管理华为云中间件资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- Kafka Topic 操作使用 `hcloud Kafka` 前缀
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Restart* | 会创建/修改/重启资源 |
| 🔴 危险 | Delete* | 不可逆操作，务必确认 |

