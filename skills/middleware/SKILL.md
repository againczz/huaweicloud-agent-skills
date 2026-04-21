---
name: huaweicloud-middleware
description: 华为云中间件服务 (Kafka/RabbitMQ/RocketMQ/CSS)。当查询大量 Topic、消费组或索引列表时，需注意 API 默认仅返回 25 条。若结果恰好为 25 条或 count 字段大于列表长度，应使用 --limit 或 --marker 获取全量。
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

