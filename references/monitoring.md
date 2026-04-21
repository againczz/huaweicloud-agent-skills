# 监控与运维服务参考

## 目录
- [CES 云监控](#ces-云监控)
- [CTS 云审计](#cts-云审计)
- [SMN 消息通知](#smn-消息通知)

---

## CES 云监控

CES (Cloud Eye Service) 提供云资源的指标监控和告警能力。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询指标列表 | `ListMetrics` | 查看可监控的指标 |
| 查询监控数据 | `ShowMetricData` | 查看指标数据 |
| 批量查询数据 | `BatchListMetricData` | 批量查询多个指标 |
| 查询告警规则 | `ListAlarms` | 列出告警规则 |
| 创建告警规则 | `CreateAlarm` | 新建告警 |
| 删除告警规则 | `DeleteAlarm` | 删除告警 |
| 启停告警 | `UpdateAlarmAction` | 启用/停用告警 |

### 查询指标

```bash
# 列出 ECS 的所有可用指标
hcloud CES ListMetrics --namespace=SYS.ECS --cli-region=cn-north-4

# 列出指定 ECS 实例的指标
hcloud CES ListMetrics \
  --namespace=SYS.ECS \
  --dim.0=instance_id,<ecs-id> \
  --cli-region=cn-north-4

# 按指标名过滤（如 CPU 利用率）
hcloud CES ListMetrics \
  --namespace=SYS.ECS \
  --metric_name=cpu_util \
  --cli-region=cn-north-4
```

### 查询监控数据

```bash
# 查询 ECS CPU 使用率（最近 1 小时，5 分钟粒度）
hcloud CES ShowMetricData \
  --namespace=SYS.ECS \
  --metric_name=cpu_util \
  --dim.0=instance_id,<ecs-id> \
  --from=$(date -v-1H +%s)000 \
  --to=$(date +%s)000 \
  --period=300 \
  --filter=average \
  --cli-region=cn-north-4

# 查询 ECS 内存使用率
hcloud CES ShowMetricData \
  --namespace=SYS.ECS \
  --metric_name=mem_util \
  --dim.0=instance_id,<ecs-id> \
  --from=$(date -v-1H +%s)000 \
  --to=$(date +%s)000 \
  --period=300 \
  --filter=average \
  --cli-region=cn-north-4

# 查询 ECS 磁盘读写
hcloud CES ShowMetricData \
  --namespace=SYS.ECS \
  --metric_name=disk_read_bytes_rate \
  --dim.0=instance_id,<ecs-id> \
  --from=$(date -v-1H +%s)000 \
  --to=$(date +%s)000 \
  --period=300 \
  --filter=average \
  --cli-region=cn-north-4

# 查询 RDS CPU 使用率
hcloud CES ShowMetricData \
  --namespace=SYS.RDS \
  --metric_name=rds001_cpu_util \
  --dim.0=rds_instance_id,<rds-id> \
  --from=$(date -v-1H +%s)000 \
  --to=$(date +%s)000 \
  --period=300 \
  --filter=average \
  --cli-region=cn-north-4
```

### 常用监控指标

| 服务 | namespace | 指标名 | 说明 |
|------|-----------|--------|------|
| ECS | SYS.ECS | cpu_util | CPU 使用率 |
| ECS | SYS.ECS | mem_util | 内存使用率 |
| ECS | SYS.ECS | disk_util_inband | 磁盘使用率 |
| ECS | SYS.ECS | network_incoming_bytes_rate_inband | 入带宽 |
| ECS | SYS.ECS | network_outgoing_bytes_rate_inband | 出带宽 |
| RDS | SYS.RDS | rds001_cpu_util | CPU 使用率 |
| RDS | SYS.RDS | rds002_mem_util | 内存使用率 |
| RDS | SYS.RDS | rds003_iops | IOPS |
| RDS | SYS.RDS | rds047_disk_util | 磁盘使用率 |
| ELB | SYS.ELB | m7_in_Bps | 入流量(Byte/s) |
| ELB | SYS.ELB | m8_out_Bps | 出流量(Byte/s) |
| DCS | SYS.DCS | cpu_usage | CPU 使用率 |
| DCS | SYS.DCS | memory_usage | 内存使用率 |

### 告警管理

```bash
# 列出所有告警规则
hcloud CES ListAlarms --cli-region=cn-north-4

# 创建告警规则（ECS CPU > 80% 持续 3 个周期触发）
hcloud CES CreateAlarm \
  --cli-jsonInput='{
    "alarm_name": "high-cpu-alert",
    "metric": {
      "namespace": "SYS.ECS",
      "metric_name": "cpu_util",
      "dimensions": [{"name": "instance_id", "value": "<ecs-id>"}]
    },
    "condition": {
      "period": 300,
      "filter": "average",
      "comparison_operator": ">=",
      "value": 80,
      "count": 3,
      "unit": "%"
    },
    "alarm_actions": [
      {
        "type": "notification",
        "notificationList": ["urn:smn:cn-north-4:<project-id>:<topic-name>"]
      }
    ],
    "alarm_enabled": true,
    "alarm_action_enabled": true
  }' \
  --cli-region=cn-north-4

# 停用告警
hcloud CES UpdateAlarmAction \
  --alarm_id=<alarm-id> \
  --cli-jsonInput='{"alarm_enabled":false}' \
  --cli-region=cn-north-4

# 启用告警
hcloud CES UpdateAlarmAction \
  --alarm_id=<alarm-id> \
  --cli-jsonInput='{"alarm_enabled":true}' \
  --cli-region=cn-north-4

# 删除告警
hcloud CES DeleteAlarm --alarm_id=<alarm-id> --cli-region=cn-north-4
```

---

## CTS 云审计

CTS (Cloud Trace Service) 记录用户对云资源的所有 API 操作，用于安全审计和问题追溯。

### 常用操作

```bash
# 查询操作记录
hcloud CTS ListTraces \
  --trace_type=system \
  --from=$(date -v-1d +%s)000 \
  --to=$(date +%s)000 \
  --cli-region=cn-north-4

# 按服务过滤（查看 ECS 相关操作）
hcloud CTS ListTraces \
  --trace_type=system \
  --service_type=ECS \
  --from=$(date -v-1d +%s)000 \
  --to=$(date +%s)000 \
  --cli-region=cn-north-4

# 按资源名过滤
hcloud CTS ListTraces \
  --trace_type=system \
  --resource_name=<resource-name> \
  --from=$(date -v-1d +%s)000 \
  --to=$(date +%s)000 \
  --cli-region=cn-north-4

# 按用户过滤
hcloud CTS ListTraces \
  --trace_type=system \
  --user=<username> \
  --from=$(date -v-1d +%s)000 \
  --to=$(date +%s)000 \
  --cli-region=cn-north-4

# 查询审计追踪器
hcloud CTS ListTrackers --cli-region=cn-north-4
```

### 审计场景
- **安全事件调查**：查看谁在什么时候删除了某个资源
- **合规审计**：定期审查关键资源的操作记录
- **故障排查**：回溯变更操作确定问题根因

---

## SMN 消息通知

SMN (Simple Message Notification) 提供主题发布/订阅模式的消息通知服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询主题列表 | `ListTopics` | 列出所有通知主题 |
| 创建主题 | `CreateTopic` | 新建通知主题 |
| 删除主题 | `DeleteTopic` | 删除主题 |
| 查询订阅列表 | `ListSubscriptions` | 列出所有订阅 |
| 添加订阅 | `CreateSubscription` | 新增订阅 |
| 发布消息 | `PublishMessage` | 向主题发送消息 |

### 操作示例

```bash
# 列出所有主题
hcloud SMN ListTopics --cli-region=cn-north-4

# 创建通知主题
hcloud SMN CreateTopic \
  --cli-jsonInput='{
    "name": "ops-alerts",
    "display_name": "运维告警"
  }' \
  --cli-region=cn-north-4

# 添加邮件订阅
hcloud SMN CreateSubscription \
  --topic_urn=urn:smn:cn-north-4:<project-id>:ops-alerts \
  --cli-jsonInput='{
    "protocol": "email",
    "endpoint": "admin@example.com",
    "remark": "运维告警邮件"
  }' \
  --cli-region=cn-north-4

# 添加短信订阅
hcloud SMN CreateSubscription \
  --topic_urn=urn:smn:cn-north-4:<project-id>:ops-alerts \
  --cli-jsonInput='{
    "protocol": "sms",
    "endpoint": "+8613800138000",
    "remark": "运维告警短信"
  }' \
  --cli-region=cn-north-4

# 添加 HTTP/HTTPS 回调订阅
hcloud SMN CreateSubscription \
  --topic_urn=urn:smn:cn-north-4:<project-id>:ops-alerts \
  --cli-jsonInput='{
    "protocol": "https",
    "endpoint": "https://hooks.example.com/alert",
    "remark": "Webhook 回调"
  }' \
  --cli-region=cn-north-4

# 发布消息
hcloud SMN PublishMessage \
  --topic_urn=urn:smn:cn-north-4:<project-id>:ops-alerts \
  --cli-jsonInput='{
    "subject": "CPU 告警",
    "message": "服务器 my-ecs CPU 使用率超过 90%"
  }' \
  --cli-region=cn-north-4

# 列出订阅
hcloud SMN ListSubscriptions --cli-region=cn-north-4

# 取消订阅
hcloud SMN DeleteSubscription --subscription_urn=<subscription-urn> --cli-region=cn-north-4

# 删除主题
hcloud SMN DeleteTopic --topic_urn=<topic-urn> --cli-region=cn-north-4
```

### 典型组合：CES 告警 + SMN 通知

监控告警和消息通知通常搭配使用：
1. 创建 SMN 主题并添加订阅（邮件/短信/Webhook）
2. 在 CES 创建告警规则时，alarm_actions 指向 SMN 主题的 URN
3. 当监控指标触发告警阈值时，自动通过 SMN 发送通知
