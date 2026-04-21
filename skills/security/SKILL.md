---
name: huaweicloud-security
description: 华为云安全与身份服务 (IAM)。当查询大量用户、用户组或策略列表时，需注意 API 默认仅返回 25 条。若结果恰好为 25 条或 count 字段大于列表长度，应使用 --limit 或 --marker 获取全量。
---

# 华为云安全与身份服务

通过 KooCLI (`hcloud`) 管理华为云身份与权限。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud IAM <Operation> [--参数=值]`
- IAM 为全局服务，部分操作无需指定 region

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | Keystone*List*, Keystone*Show*, List* | 只读查询 |
| 🟡 注意 | Keystone*Create*, Create*, Update* | 创建/修改用户和权限 |
| 🔴 危险 | Keystone*Delete*, Delete* | 删除用户/密钥不可逆 |

> ⚠️ 切勿在日志中记录 AK/SK 等敏感凭据

