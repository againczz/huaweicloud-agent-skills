# 监控与运维服务参考

## 目录
- [CES 云监控](#ces-云监控)
- [CTS 云审计](#cts-云审计)
- [SMN 消息通知](#smn-消息通知)
- [LTS 云日志服务](#lts-云日志服务)

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

---

## LTS 云日志服务

LTS (Log Tank Service) 提供日志采集、存储、查询和分析能力，支持 ECS、CCE、FunctionGraph 等多种日志源的统一管理。

### 核心概念

- **日志组 (Log Group)**：日志的逻辑分组，用于隔离和管理不同业务的日志
- **日志流 (Log Stream)**：日志组内的日志通道，对应一类具体的日志来源
- **日志接入**：将 ECS 主机日志、容器日志等采集到 LTS

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询日志组列表 | `ListLogGroups` | 列出所有日志组 |
| 查询日志组详情 | `ShowLogGroup` | 查看单个日志组 |
| 创建日志组 | `CreateLogGroup` | 新建日志组 |
| 删除日志组 | `DeleteLogGroup` | 删除日志组 |
| 查询日志流列表 | `ListLogStreams` | 列出日志流 |
| 创建日志流 | `CreateLogStream` | 新建日志流 |
| 删除日志流 | `DeleteLogStream` | 删除日志流 |
| 查询日志 | `ListLogs` | 搜索日志内容 |
| 查询结构化配置 | `ListStructuredLogs` | 查看结构化解析规则 |
| 查询日志转储 | `ListTransfers` | 查看日志转储任务 |
| 创建日志转储 | `CreateTransfer` | 新建转储到 OBS |
| 查询主机组 | `ListHostGroups` | 查看日志采集主机组 |
| 查询接入规则 | `ListAccessConfig` | 查看日志接入配置 |

### 日志组管理

```bash
# 列出所有日志组
hcloud LTS ListLogGroups --cli-region=cn-north-4

# 精简输出
hcloud LTS ListLogGroups \
  --cli-query="log_groups[].{ID:log_group_id,Name:log_group_name,TTL:ttl_in_days}" \
  --cli-region=cn-north-4

# 查看日志组详情
hcloud LTS ShowLogGroup --log_group_id=<group-id> --cli-region=cn-north-4

# 创建日志组（保留 7 天）
hcloud LTS CreateLogGroup \
  --cli-jsonInput='{
    "log_group_name": "my-app-logs",
    "ttl_in_days": 7
  }' \
  --cli-region=cn-north-4

# 创建日志组（保留 30 天）
hcloud LTS CreateLogGroup \
  --cli-jsonInput='{
    "log_group_name": "prod-audit-logs",
    "ttl_in_days": 30
  }' \
  --cli-region=cn-north-4

# 修改日志组保留天数
hcloud LTS UpdateLogGroup \
  --log_group_id=<group-id> \
  --cli-jsonInput='{"ttl_in_days": 30}' \
  --cli-region=cn-north-4

# 删除日志组（会同时删除组内所有日志流和日志数据！）
hcloud LTS DeleteLogGroup --log_group_id=<group-id> --cli-region=cn-north-4
```

### 日志流管理

```bash
# 列出指定日志组下的日志流
hcloud LTS ListLogStreams \
  --log_group_id=<group-id> \
  --cli-region=cn-north-4

# 精简输出
hcloud LTS ListLogStreams \
  --log_group_id=<group-id> \
  --cli-query="log_streams[].{ID:log_stream_id,Name:log_stream_name}" \
  --cli-region=cn-north-4

# 创建日志流
hcloud LTS CreateLogStream \
  --log_group_id=<group-id> \
  --cli-jsonInput='{
    "log_stream_name": "nginx-access"
  }' \
  --cli-region=cn-north-4

# 删除日志流
hcloud LTS DeleteLogStream \
  --log_group_id=<group-id> \
  --log_stream_id=<stream-id> \
  --cli-region=cn-north-4
```

### 日志查询

```bash
# 查询日志（最近 1 小时）
hcloud LTS ListLogs \
  --log_group_id=<group-id> \
  --log_stream_id=<stream-id> \
  --cli-jsonInput='{
    "start_time": "'$(date -v-1H +%s)'000",
    "end_time": "'$(date +%s)'000",
    "limit": 100
  }' \
  --cli-region=cn-north-4

# 关键词搜索
hcloud LTS ListLogs \
  --log_group_id=<group-id> \
  --log_stream_id=<stream-id> \
  --cli-jsonInput='{
    "start_time": "'$(date -v-1H +%s)'000",
    "end_time": "'$(date +%s)'000",
    "keywords": "ERROR",
    "limit": 50
  }' \
  --cli-region=cn-north-4

# 结构化查询（SQL 模式）
hcloud LTS ListLogs \
  --log_group_id=<group-id> \
  --log_stream_id=<stream-id> \
  --cli-jsonInput='{
    "start_time": "'$(date -v-1H +%s)'000",
    "end_time": "'$(date +%s)'000",
    "is_analysis": true,
    "analyze_statement": "SELECT status, count(*) as cnt GROUP BY status ORDER BY cnt DESC",
    "limit": 100
  }' \
  --cli-region=cn-north-4
```

### 日志转储到 OBS

```bash
# 查看所有转储任务
hcloud LTS ListTransfers --cli-region=cn-north-4

# 创建日志转储到 OBS（推荐使用 skeleton）
hcloud LTS CreateTransfer --skeleton > create_transfer.json
# 关键参数：
# - log_group_id: 日志组 ID
# - log_streams[].log_stream_id: 日志流 ID
# - log_transfer_info.log_storage_format: json 或 raw
# - log_transfer_info.log_transfer_type: OBS
# - log_transfer_info.obs_bucket_name: OBS 桶名
# - log_transfer_info.obs_dir_prefix_name: 目录前缀
# - log_transfer_info.period: 转储周期(分钟)
hcloud LTS CreateTransfer --cli-jsonInput=file://create_transfer.json --cli-region=cn-north-4
```

### 日志接入（主机日志采集）

```bash
# 查看主机组
hcloud LTS ListHostGroups --cli-region=cn-north-4

# 查看接入配置
hcloud LTS ListAccessConfig --cli-region=cn-north-4

# 创建接入配置（推荐 skeleton）
hcloud LTS CreateAccessConfig --skeleton > create_access.json
# 关键参数：
# - access_config_name: 接入名称
# - access_config_type: AGENT（主机日志）
# - log_info.log_group_id: 目标日志组
# - log_info.log_stream_id: 目标日志流
# - access_config_detail.paths: 采集路径（如 ["/var/log/nginx/*.log"]）
hcloud LTS CreateAccessConfig --cli-jsonInput=file://create_access.json --cli-region=cn-north-4
```

### 常见日志保留策略

| 场景 | ttl_in_days | 说明 |
|------|-------------|------|
| 开发测试 | 3-7 | 短期调试，节省成本 |
| 生产运维 | 30 | 日常排查需要 |
| 安全审计 | 90-180 | 合规要求 |
| 长期归档 | 365 | 配合 OBS 转储 |

### 最佳实践
- 按业务或应用划分日志组，同一应用的不同类型日志（access / error / app）用不同日志流
- 生产环境启用结构化解析，便于 SQL 分析和告警
- 大量日志数据建议配置 OBS 转储，降低存储成本
- 配合 CES 告警、SMN 通知实现日志关键词告警

### 典型组合：LTS + CES + SMN 日志告警

1. 在 LTS 中创建日志组和日志流，配置主机日志采集
2. 设置结构化解析，将关键字段（如 status、level）提取为指标
3. 在 CES 中基于 LTS 日志指标创建告警规则
4. 通过 SMN 发送告警通知（邮件/短信/Webhook）

