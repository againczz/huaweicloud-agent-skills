---
name: huaweicloud-networking
description: 华为云网络服务 (VPC/ELB/NAT/EIP/DNS)。当结果包含大量资源（如大量私有 IP 或安全组规则）时，需注意华为云 API 默认仅返回 25 条。若结果恰好为 25 条或 count 字段大于列表长度，应使用 --limit 或 --marker 获取全量。
---

# 华为云网络服务

通过 KooCLI (`hcloud`) 管理华为云网络资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Add* | 会创建/修改资源，注意参数正确性 |
| 🔴 危险 | Delete*, Batch*Delete* | 不可逆操作，务必确认 |

