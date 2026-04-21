# 计算服务参考

## 目录
- [ECS 弹性云服务器](#ecs-弹性云服务器)
- [BMS 裸金属服务器](#bms-裸金属服务器)
- [AS 弹性伸缩](#as-弹性伸缩)
- [FunctionGraph 函数工作流](#functiongraph-函数工作流)

---

## ECS 弹性云服务器

弹性云服务器 (Elastic Cloud Server) 是华为云最核心的计算服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询服务器列表 | `ListServersDetails` | 获取所有 ECS 实例详情 |
| 查询单台服务器 | `ShowServer` | 按 ID 查询服务器详情 |
| 创建服务器 | `CreateServers` | 创建一台或多台 ECS |
| 删除服务器 | `DeleteServers` | 删除指定 ECS |
| 启动服务器 | `BatchStartServers` | 批量启动 |
| 停止服务器 | `BatchStopServers` | 批量停止 |
| 重启服务器 | `BatchRebootServers` | 批量重启 |
| 修改服务器 | `UpdateServer` | 修改名称等属性 |
| 变更规格 | `ResizeServer` | 升降配（需关机） |
| 查询规格列表 | `ListFlavors` | 查看可用规格 |

### 分页与统计 (Agent 技巧)

> [!IMPORTANT]
> **默认限制**：华为云查询接口默认只返回 **25** 条。
> **处理建议**：先统计总数，再按需拉取。

```bash
# 智能统计总数 (使用 JMESPath)
hcloud ECS ListServersDetails --cli-query="count"

# 获取全量列表 (若总数 > 25)
hcloud ECS ListServersDetails --limit=1000

# 检查是否还有更多 (根据 marker)
hcloud ECS ListServersDetails --limit=100
# 若响应中有 "servers_links"，则说明有下一页
```

### 常用命令示例

```bash
# 过滤指定名称
hcloud ECS ListServersDetails --name=my-server

# 按状态过滤（ACTIVE/SHUTOFF/ERROR）
hcloud ECS ListServersDetails --status=ACTIVE

# 精简输出关键字段
hcloud ECS ListServersDetails --cli-query="servers[].{ID:id,Name:name,Status:status,IP:addresses.*[0].addr}"
```

---

## BMS 裸金属服务器

裸金属服务器提供专属物理服务器。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询服务器列表 | `ListBareMetalServers` | 获取所有 BMS 实例 |
| 查询详情列表 | `ListBareMetalServersDetail` | 获取详细信息 |
| 查询单台详情 | `ShowBaremetalServer` | 按 ID 查询 |
| 修改名称 | `ChangeBaremetalServerName` | 修改 BMS 名称 |
| 启动服务器 | `BatchStartBaremetalServers` | 批量启动 |
| 停止服务器 | `BatchStopBaremetalServers` | 批量停止 |

```bash
# 查询 BMS 列表并统计
hcloud BMS ListBareMetalServers --cli-query="length(servers)"
```

---

## AS 弹性伸缩

弹性伸缩 (Auto Scaling) 自动调整计算资源。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询伸缩组 | `ListScalingGroups` | 列出所有伸缩组 |
| 查询伸缩配置 | `ListScalingConfigs` | 列出伸缩配置 |
| 查询伸缩实例 | `ListScalingInstances` | 查看组内成员 |
| 查询活动日志 | `ListScalingActivityLogs` | 查看伸缩历史 |
| 执行策略 | `ExecuteScalingPolicy` | 手动触发伸缩 |

```bash
# 查看伸缩活动详情
hcloud AS ListScalingActivityLogs --scaling_group_id=<group-id> --cli-query="scaling_activity_log"

# 启用伸缩组
hcloud AS EnableOrDisableScalingGroup \
  --scaling_group_id=<group-id> \
  --cli-jsonInput='{"action":"resume"}'
```

---

## FunctionGraph 函数工作流

FunctionGraph 是华为云的 Serverless 计算服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询函数列表 | `ListFunctions` | 列出所有函数 |
| 查询函数详情 | `ShowFunction` | 查看配置及代码 |
| 更新代码 | `UpdateFunctionCode` | 部署新代码包 |
| 同步调用 | `InvokeFunction` | 立即执行 |
| 查询指标 | `ListFunctionStatistics` | 查看运行统计 |

### 操作示例

```bash
# 调用函数并输出结果
hcloud FunctionGraph InvokeFunction --function_urn=<urn> --cli-jsonInput='{}'

# 查看执行统计
hcloud FunctionGraph ListFunctionStatistics --func_urn=<urn> --period=300
```

### 最佳实践
- 在代码中避免长时间阻塞操作
- 设置合理的超时时间
- 使用环境变量管理配置
- 配合 API 网关或 OBS 触发器构建事件驱动架构
