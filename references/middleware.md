# 中间件服务参考

## 目录
- [DMS 分布式消息服务](#dms-分布式消息服务)
- [CSS 云搜索服务](#css-云搜索服务)

---

## DMS 分布式消息服务

DMS (Distributed Message Service) 提供 Kafka 和 RabbitMQ 两种消息中间件实例。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 列出所有 DMS 实例 |
| 查询实例详情 | `ShowInstance` | 查看单个实例 |
| 创建 Kafka 实例 | `CreateInstance` | 新建 Kafka 实例 |
| 删除实例 | `DeleteInstance` | 删除实例 |
| 查询 Topic 列表 | `ListTopics` (Kafka) | 列出 Kafka Topic |
| 创建 Topic | `CreateTopic` (Kafka) | 新建 Topic |
| 重启实例 | `RestartManager` | 重启实例管理进程 |

### Kafka 实例管理

```bash
# 列出所有 Kafka 实例
hcloud DMS ListInstances --engine=kafka --cli-region=cn-north-4

# 精简输出
hcloud DMS ListInstances \
  --engine=kafka \
  --cli-query="instances[].{ID:instance_id,Name:name,Status:status,Version:engine_version}" \
  --cli-region=cn-north-4

# 查看 Kafka 实例详情
hcloud DMS ShowInstance --instance_id=<instance-id> --cli-region=cn-north-4

# 创建 Kafka 实例（推荐 skeleton）
hcloud DMS CreateInstance --skeleton > create_kafka.json
# 关键参数：
# - name: 实例名称
# - engine: kafka
# - engine_version: 2.7（或其他版本）
# - storage_space: 存储空间(GB)
# - vpc_id / subnet_id / security_group_id: 网络配置
# - product_id: 规格 ID
hcloud DMS CreateInstance --cli-jsonInput=file://create_kafka.json --cli-region=cn-north-4

# 删除实例
hcloud DMS DeleteInstance --instance_id=<instance-id> --cli-region=cn-north-4
```

### Kafka Topic 管理

```bash
# 列出 Topic
hcloud Kafka ListInstanceTopics --instance_id=<instance-id> --cli-region=cn-north-4

# 创建 Topic
hcloud Kafka CreateInstanceTopic \
  --instance_id=<instance-id> \
  --cli-jsonInput='{
    "id": "my-topic",
    "partition": 3,
    "replication": 3,
    "retention_time": 72
  }' \
  --cli-region=cn-north-4

# 修改 Topic 配置
hcloud Kafka UpdateInstanceTopic \
  --instance_id=<instance-id> \
  --cli-jsonInput='{
    "topics": [{
      "id": "my-topic",
      "partition": 6,
      "retention_time": 168
    }]
  }' \
  --cli-region=cn-north-4

# 删除 Topic
hcloud Kafka DeleteInstanceTopic \
  --instance_id=<instance-id> \
  --cli-jsonInput='{"topics":["my-topic"]}' \
  --cli-region=cn-north-4

# 查询消费组
hcloud Kafka ListInstanceConsumerGroups --instance_id=<instance-id> --cli-region=cn-north-4
```

### RabbitMQ 实例管理

```bash
# 列出 RabbitMQ 实例
hcloud DMS ListInstances --engine=rabbitmq --cli-region=cn-north-4

# 查看实例详情
hcloud DMS ShowInstance --instance_id=<instance-id> --cli-region=cn-north-4

# 创建 RabbitMQ 实例
hcloud DMS CreateInstance --skeleton > create_rabbitmq.json
hcloud DMS CreateInstance --cli-jsonInput=file://create_rabbitmq.json --cli-region=cn-north-4
```

### 最佳实践
- Kafka Topic 的 partition 数量决定消费并发度，根据业务流量合理设置
- replication 设置为 3 确保数据高可用
- 合理设置消息保留时间(retention_time)，避免磁盘写满
- 生产环境使用跨 AZ 部署提高可用性

---

## CSS 云搜索服务

CSS (Cloud Search Service) 提供托管的 Elasticsearch 集群。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询集群列表 | `ListClusters` | 列出所有 CSS 集群 |
| 查询集群详情 | `ShowCluster` | 查看单个集群 |
| 创建集群 | `CreateCluster` | 新建 ES 集群 |
| 删除集群 | `DeleteCluster` | 删除集群 |
| 重启集群 | `RestartCluster` | 重启集群 |
| 扩容集群 | `UpdateExtendCluster` | 扩容节点/存储 |

### 操作示例

```bash
# 列出所有 CSS 集群
hcloud CSS ListClusters --cli-region=cn-north-4

# 精简输出
hcloud CSS ListClusters \
  --cli-query="clusters[].{ID:id,Name:name,Status:status,Version:datastore.version}" \
  --cli-region=cn-north-4

# 查看集群详情
hcloud CSS ShowCluster --cluster_id=<cluster-id> --cli-region=cn-north-4

# 创建 ES 集群
hcloud CSS CreateCluster \
  --cli-jsonInput='{
    "cluster": {
      "name": "my-es-cluster",
      "datastore": {
        "type": "elasticsearch",
        "version": "7.10.2"
      },
      "instanceNum": 3,
      "instance": {
        "flavorRef": "<flavor-id>",
        "volume": {
          "volume_type": "COMMON",
          "size": 100
        },
        "nics": {
          "vpcId": "<vpc-id>",
          "netId": "<subnet-id>",
          "securityGroupId": "<sg-id>"
        },
        "availability_zone": "cn-north-4a"
      }
    }
  }' \
  --cli-region=cn-north-4

# 重启集群
hcloud CSS RestartCluster --cluster_id=<cluster-id> --cli-region=cn-north-4

# 扩容集群节点
hcloud CSS UpdateExtendCluster \
  --cluster_id=<cluster-id> \
  --cli-jsonInput='{"grow":[{"type":"ess","nodesize":2,"disksize":0}]}' \
  --cli-region=cn-north-4

# 删除集群
hcloud CSS DeleteCluster --cluster_id=<cluster-id> --cli-region=cn-north-4
```

### 最佳实践
- 生产环境使用 3 节点以上保证高可用
- 数据节点和 Master 节点分开部署
- 定期创建快照备份数据
- 合理设置 index 生命周期管理策略
