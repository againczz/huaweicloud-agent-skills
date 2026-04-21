# 数据库服务参考

## 目录
- [RDS 关系型数据库](#rds-关系型数据库)
- [DDS 文档数据库](#dds-文档数据库)
- [DCS 分布式缓存](#dcs-分布式缓存)
- [GaussDB](#gaussdb)

---

## RDS 关系型数据库

RDS 提供托管的 MySQL、PostgreSQL、SQL Server、MariaDB 数据库实例。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 列出所有 RDS 实例 |
| 查询实例详情 | `ShowInstance` | 查看单个实例 |
| 创建实例 | `CreateInstance` | 新建数据库实例 |
| 删除实例 | `DeleteInstance` | 删除实例 |
| 重启实例 | `RestartInstance` | 重启数据库 |
| 变更规格 | `ResizeInstance` | 升降配 |
| 扩容存储 | `EnlargeVolume` | 扩大存储空间 |
| 查询备份列表 | `ListBackups` | 查看所有备份 |
| 创建备份 | `CreateManualBackup` | 手动备份 |
| 查询慢日志 | `ListSlowLogs` | 查看慢查询 |

### 查询操作

```bash
# 列出所有 RDS 实例
hcloud RDS ListInstances --cli-region=cn-north-4

# 精简输出
hcloud RDS ListInstances \
  --cli-query="instances[].{ID:id,Name:name,Engine:datastore.type,Status:status}" \
  --cli-region=cn-north-4

# 查看实例详情
hcloud RDS ShowInstance --instance_id=<instance-id> --cli-region=cn-north-4

# 查询可用规格
hcloud RDS ListFlavors --database_name=MySQL --cli-region=cn-north-4

# 查看备份列表
hcloud RDS ListBackups --instance_id=<instance-id> --cli-region=cn-north-4
```

### 创建实例

```bash
# 生成参数模板
hcloud RDS CreateInstance --skeleton > create_rds.json

# 手动创建 MySQL 实例
hcloud RDS CreateInstance \
  --cli-jsonInput='{
    "name": "my-mysql",
    "datastore": {
      "type": "MySQL",
      "version": "8.0"
    },
    "flavor_ref": "<flavor-id>",
    "volume": {
      "type": "ULTRAHIGH",
      "size": 100
    },
    "region": "cn-north-4",
    "availability_zone": "cn-north-4a",
    "vpc_id": "<vpc-id>",
    "subnet_id": "<subnet-id>",
    "security_group_id": "<sg-id>",
    "password": "<your-password>",
    "port": "3306"
  }' \
  --cli-region=cn-north-4
```

### 运维操作

```bash
# 重启实例
hcloud RDS RestartInstance \
  --instance_id=<instance-id> \
  --cli-jsonInput='{}' \
  --cli-region=cn-north-4

# 扩容存储
hcloud RDS EnlargeVolume \
  --instance_id=<instance-id> \
  --cli-jsonInput='{"enlarge_volume":{"size":200}}' \
  --cli-region=cn-north-4

# 手动备份
hcloud RDS CreateManualBackup \
  --cli-jsonInput='{
    "instance_id": "<instance-id>",
    "name": "manual-backup-001"
  }' \
  --cli-region=cn-north-4

# 查询慢日志
hcloud RDS ListSlowLogs \
  --instance_id=<instance-id> \
  --start_date=2026-04-20T00:00:00+0800 \
  --end_date=2026-04-21T00:00:00+0800 \
  --cli-region=cn-north-4

# 删除实例
hcloud RDS DeleteInstance --instance_id=<instance-id> --cli-region=cn-north-4
```

### 最佳实践
- 生产环境创建主备实例
- 启用自动备份并设置合理的保留策略
- 使用独立的安全组，仅开放数据库端口
- 定期检查慢日志优化查询

---

## DDS 文档数据库

DDS（Document Database Service）提供 MongoDB 兼容的文档数据库。

### 常用操作

```bash
# 列出所有 DDS 实例
hcloud DDS ListInstances --cli-region=cn-north-4

# 精简输出
hcloud DDS ListInstances \
  --cli-query="instances[].{ID:id,Name:name,Mode:mode,Status:status}" \
  --cli-region=cn-north-4

# 查看实例详情
hcloud DDS ShowInstance --instance_id=<instance-id> --cli-region=cn-north-4

# 创建实例（推荐使用 skeleton）
hcloud DDS CreateInstance --skeleton > create_dds.json
hcloud DDS CreateInstance --cli-jsonInput=file://create_dds.json --cli-region=cn-north-4

# 重启实例
hcloud DDS RestartInstance \
  --instance_id=<instance-id> \
  --cli-jsonInput='{"target_id":"<instance-id>"}' \
  --cli-region=cn-north-4

# 扩容存储
hcloud DDS ResizeInstanceVolume \
  --instance_id=<instance-id> \
  --cli-jsonInput='{"volume":{"size":200}}' \
  --cli-region=cn-north-4

# 查看备份
hcloud DDS ListBackups --instance_id=<instance-id> --cli-region=cn-north-4

# 删除实例
hcloud DDS DeleteInstance --instance_id=<instance-id> --cli-region=cn-north-4
```

---

## DCS 分布式缓存

DCS 提供 Redis 和 Memcached 兼容的缓存服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 列出所有缓存实例 |
| 查询实例详情 | `ShowInstance` | 查看单个实例 |
| 创建实例 | `CreateInstance` | 新建缓存实例 |
| 删除实例 | `DeleteSingleInstance` | 删除单个实例 |
| 重启实例 | `RestartInstance` | 重启缓存实例 |
| 变更规格 | `ResizeInstance` | 升降配 |
| 查询备份 | `ListBackupRecords` | 查看备份记录 |

### 操作示例

```bash
# 列出所有缓存实例
hcloud DCS ListInstances --cli-region=cn-north-4

# 精简输出
hcloud DCS ListInstances \
  --cli-query="instances[].{ID:instance_id,Name:name,Engine:engine,Status:status}" \
  --cli-region=cn-north-4

# 查看实例详情
hcloud DCS ShowInstance --instance_id=<instance-id> --cli-region=cn-north-4

# 创建 Redis 实例
hcloud DCS CreateInstance \
  --cli-jsonInput='{
    "name": "my-redis",
    "engine": "Redis",
    "engine_version": "6.0",
    "capacity": 2,
    "spec_code": "redis.ha.xu1.large.r2.2",
    "vpc_id": "<vpc-id>",
    "subnet_id": "<subnet-id>",
    "security_group_id": "<sg-id>",
    "password": "<your-password>",
    "no_password_access": false,
    "available_zones": ["<az-id>"]
  }' \
  --cli-region=cn-north-4

# 重启实例
hcloud DCS RestartInstance \
  --instance_id=<instance-id> \
  --cli-jsonInput='{"instances":["<instance-id>"]}' \
  --cli-region=cn-north-4

# 查看慢日志
hcloud DCS ListSlowlog \
  --instance_id=<instance-id> \
  --start_time=2026-04-20T00:00:00.000Z \
  --end_time=2026-04-21T00:00:00.000Z \
  --cli-region=cn-north-4

# 删除实例
hcloud DCS DeleteSingleInstance --instance_id=<instance-id> --cli-region=cn-north-4
```

---

## GaussDB

GaussDB 是华为自研的企业级分布式数据库，支持 GaussDB(for MySQL) 和 GaussDB(for openGauss)。

### 常用操作

```bash
# 列出 GaussDB for MySQL 实例
hcloud GaussDB ListGaussMySqlInstances --cli-region=cn-north-4

# 查看实例详情
hcloud GaussDB ShowGaussMySqlInstance \
  --instance_id=<instance-id> \
  --cli-region=cn-north-4

# 查询可用规格
hcloud GaussDB ListGaussMySqlFlavors \
  --database_name=gaussdb-mysql \
  --cli-region=cn-north-4

# 创建实例（推荐使用 skeleton）
hcloud GaussDB CreateGaussMySqlInstance --skeleton > create_gaussdb.json
hcloud GaussDB CreateGaussMySqlInstance --cli-jsonInput=file://create_gaussdb.json --cli-region=cn-north-4

# 查看备份
hcloud GaussDB ListGaussMySqlBackups --instance_id=<instance-id> --cli-region=cn-north-4

# 删除实例
hcloud GaussDB DeleteGaussMySqlInstance --instance_id=<instance-id> --cli-region=cn-north-4
```
