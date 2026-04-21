---
name: huaweicloud-monitoring
description: 华为云监控与运维服务 (CES/CTS/SMN/LTS)。当用户提到云监控、CES、CPU使用率、内存监控、指标、告警、告警规则、云审计、CTS、操作记录、审计日志、消息通知、SMN、订阅、邮件通知、短信通知、云日志、LTS、日志组、日志流、日志查询、日志采集、日志转储时触发。
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

