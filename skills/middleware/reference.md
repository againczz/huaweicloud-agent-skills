# 中间件服务参考

## 目录
- [Kafka 消息队列](#kafka-消息队列)
- [RabbitMQ 消息队列](#rabbitmq-消息队列)
- [RocketMQ 消息队列](#rocketmq-消息队列)
- [CSS 云搜索服务](#css-云搜索服务)

---

## Kafka 消息队列

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有 Kafka 实例 |
| 查询 Topic 列表 | `ListTopics` | 查看实例下的 Topic |
| 查询消费组 | `ListGroups` | 查看消费组详情 |
| 创建 Topic | `CreateTopic` | 新建 Kafka Topic |
| 删除 Topic | `DeleteTopic` | 删除指定 Topic |

### 分页与统计 (Agent 技巧)

```bash
# 智能统计 Topic 数量
hcloud Kafka ListTopics --instance_id=<id> --cli-query="length(topics)"

# 列出所有消费组 (默认分页 25)
hcloud Kafka ListGroups --instance_id=<id> --limit=100
```

---

## RabbitMQ 消息队列

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有 RabbitMQ 实例 |
| 查询 Vhost | `ListVhosts` | 查看虚拟主机列表 |
| 查询 Queue | `ListQueues` | 查看队列详情 |
| 创建实例 | `CreateInstanceByEngine` | 购买 RabbitMQ 实例 |

---

## RocketMQ 消息队列

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有 RocketMQ 实例 |
| 查询 Topic | `ListRocketInstanceTopics` | 查看主题详情 |
| 查询消费组 | `ListInstanceConsumerGroups` | 查看消费组 |
| 发送测试消息 | `SendMessage` | 向指定 Topic 发送消息 |

---

## CSS 云搜索服务

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询集群列表 | `ListClustersDetails` | 获取 CSS/ES 集群详情 |
| 查询快照 | `ListSnapshots` | 查看索引快照 |
| 重启集群 | `RollingRestart` | 滚动重启集群 |
| 获取负载均衡详情 | `ShowElbDetail` | 查看绑定的 ELB 信息 |

```bash
# 查询 CSS 集群状态及节点数
hcloud CSS ListClustersDetails --cli-query="clusters[].{Name:name, Nodes:nodeNum, Status:status}"
```
