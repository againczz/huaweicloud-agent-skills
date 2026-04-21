# 数据库服务参考

## 目录
- [RDS 关系型数据库](#rds-关系型数据库)
- [DDS 文档数据库](#dds-文档数据库)
- [DCS 分布式缓存](#dcs-分布式缓存)
- [GaussDB 数据库](#gaussdb-数据库)

---

## RDS 关系型数据库

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有数据库实例 |
| 查询实例详情 | `ShowInstance` | 按 ID 查询详情 |
| 查询备份列表 | `ListBackups` | 获取备份详情 |
| 创建实例 | `CreateInstance` | 购买新的数据库 |
| 删除实例 | `DeleteInstance` | 释放实例 |
| 重启实例 | `RestartInstance` | 重启数据库进程 |
| 变更规格 | `ResizeInstance` | 调大/调小规格 |

### 分页与统计 (Agent 技巧)

```bash
# 智能统计实例总数
hcloud RDS ListInstances --cli-query="total_count"

# 列出所有备份 (备份通常很多，注意分页)
hcloud RDS ListBackups --limit=100
```

---

## DDS 文档数据库

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有 DDS 实例 |
| 查询详情 | `ShowInstance` | 查看实例详细配置 |
| 创建实例 | `CreateInstance` | 创建文档数据库 |
| 扩容存储 | `EnlargeInstanceVolume` | 增加磁盘空间 |

---

## DCS 分布式缓存

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListInstances` | 获取所有 Redis/Memcached |
| 查看实例详情 | `ShowInstance` | 查看规格、连接地址 |
| 修改密码 | `UpdatePassword` | 变更访问密码 |
| 重启实例 | `RestartOrFlushInstances` | 重启或清空缓存 |

---

## GaussDB 数据库

华为云自研分布式数据库。

### 常用操作速查 (GaussDB for MySQL)

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询实例列表 | `ListGaussMySqlInstances` | 获取实例列表 |
| 查询详情 | `ShowGaussMySqlInstanceInfo` | 查看详细配置 |
| 重启实例 | `RestartGaussMySqlInstance` | 重启实例 |
| 修改参数 | `UpdateInstanceConfigurations` | 变更数据库参数 |

```bash
# 查询 GaussDB 实例状态
hcloud GaussDB ListGaussMySqlInstances --cli-query="instances[].{Name:name, Status:status}"
```
