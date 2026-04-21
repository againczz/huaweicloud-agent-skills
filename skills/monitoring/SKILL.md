---
name: huaweicloud-monitoring
description: 华为云监控与运维服务 (CES/CTS/SMN/LTS)。当查询历史指标数据、审计日志或日志流时，数据量可能极大，需注意 API 默认分页限制。若结果恰好为 25 条或包含 marker/next_marker，应使用 --limit 或翻页参数获取全量。
---

# 华为云监控与运维服务

通过 KooCLI (`hcloud`) 管理华为云监控与运维资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Publish* | 会创建告警/发送通知 |
| 🔴 危险 | Delete* | 删除告警规则/主题不可逆 |

