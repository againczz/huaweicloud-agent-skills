# 监控与运维参考

## 目录
- [CES 云监控](#ces-云监控)
- [CTS 云审计](#cts-云审计)
- [SMN 消息通知](#smn-消息通知)
- [LTS 云日志服务](#lts-云日志服务)

---

## CES 云监控

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询指标列表 | `ListMetrics` | 获取所有监控指标 |
| 查询监控数据 | `ShowMetricData` | 获取指定指标的历史数值 |
| 查询告警规则 | `ListAlarms` | 查看已配置的告警 |
| 创建告警规则 | `CreateAlarm` | 新建监控告警 |

### 分页与统计 (Agent 技巧)

```bash
# 列出所有指标 (指标数量通常极大，务必分页)
hcloud CES ListMetrics --limit=100

# 查询特定实例的指标
hcloud CES ListMetrics --dim.0=instance_id,<ecs-id>
```

---

## CTS 云审计

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询审计日志 | `ListTraces` | 查询资源操作记录 |
| 追踪器管理 | `ListTrackers` | 查看审计追踪器配置 |

```bash
# 查询过去 1 小时内删除 ECS 的记录
hcloud CTS ListTraces --service_type=ECS --resource_type=server --trace_name=deleteServer
```

---

## SMN 消息通知

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询主题列表 | `ListTopics` | 获取所有通知主题 |
| 查询订阅者 | `ListSubscriptions` | 查看主题下的订阅列表 |
| 发布消息 | `PublishMessage` | 向主题发送通知消息 |
| 创建主题 | `CreateTopic` | 新建消息主题 |

---

## LTS 云日志服务

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询日志组 | `ListLogGroups` | 获取日志组列表 |
| 查询日志流 | `ListLogStream` | 获取组下所有日志流 |
| 检索日志内容 | `ListLogs` | 搜索具体日志条目 |
| 查询图表/柱状图 | `ListLogHistogram` | 查看日志分布统计 |

### 分页与统计 (Agent 技巧)

```bash
# 智能检索最近 50 条日志
hcloud LTS ListLogs --log_group_id=<group-id> --log_stream_id=<stream-id> --limit=50

# 统计日志组数量
hcloud LTS ListLogGroups --cli-query="length(log_groups)"
```
