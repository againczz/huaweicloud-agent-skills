---
name: huaweicloud-database
description: 华为云数据库服务 (RDS/DDS/DCS/GaussDB)。当用户提到关系型数据库、RDS、MySQL、PostgreSQL、文档数据库、DDS、MongoDB、分布式缓存、DCS、Redis、Memcached、GaussDB、高斯数据库、数据库备份、慢日志、数据库扩容时触发。
---

# 华为云数据库服务

通过 KooCLI (`hcloud`) 管理华为云数据库资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Restart* | 会创建/修改/重启资源 |
| 🔴 危险 | Delete* | 不可逆操作，务必确认 |

