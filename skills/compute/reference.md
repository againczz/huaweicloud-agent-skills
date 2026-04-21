# 计算服务参考

## 目录
- [ECS 弹性云服务器](#ecs-弹性云服务器)
- [BMS 裸金属服务器](#bms-裸金属服务器)
- [AS 弹性伸缩](#as-弹性伸缩)
- [FunctionGraph 函数工作流](#functiongraph-函数工作流)

---

## ECS 弹性云服务器

弹性云服务器 (Elastic Cloud Server) 是华为云最核心的计算服务，提供可弹性伸缩的虚拟服务器。

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
| 变更规格 | `ResizeServer` | 升降配 |
| 查询规格列表 | `ListFlavors` | 查看可用规格 |
| 查询可用区 | `NovaListAvailabilityZones` | 查看可用区 |

### 查询操作

```bash
# 列出所有 ECS 实例
hcloud ECS ListServersDetails --cli-region=cn-north-4

# 过滤指定名称的服务器
hcloud ECS ListServersDetails --name=my-server --cli-region=cn-north-4

# 按状态过滤（ACTIVE/SHUTOFF/ERROR）
hcloud ECS ListServersDetails --status=ACTIVE --cli-region=cn-north-4

# 查看单台服务器详情
hcloud ECS ShowServer --server_id=<server-id> --cli-region=cn-north-4

# JMESPath 精简输出
hcloud ECS ListServersDetails \
  --cli-query="servers[].{ID:id,Name:name,Status:status,IP:addresses}" \
  --cli-region=cn-north-4

# 查询可用规格
hcloud ECS ListFlavors --cli-region=cn-north-4

# 查看可用区
hcloud ECS NovaListAvailabilityZones --cli-region=cn-north-4
```

### 生命周期操作

```bash
# 启动服务器
hcloud ECS BatchStartServers \
  --cli-jsonInput='{"os-start":{"servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4

# 停止服务器（优雅关机）
hcloud ECS BatchStopServers \
  --cli-jsonInput='{"os-stop":{"type":"SOFT","servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4

# 强制停止
hcloud ECS BatchStopServers \
  --cli-jsonInput='{"os-stop":{"type":"HARD","servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4

# 重启服务器
hcloud ECS BatchRebootServers \
  --cli-jsonInput='{"reboot":{"type":"SOFT","servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4
```

### 创建服务器

```bash
# 生成创建参数模板
hcloud ECS CreateServers --skeleton > create_ecs.json

# 编辑 create_ecs.json，填入以下关键字段：
# - server.name: 服务器名称
# - server.flavorRef: 规格 ID
# - server.imageRef: 镜像 ID
# - server.vpcid: VPC ID
# - server.nics[].subnet_id: 子网 ID
# - server.root_volume: 系统盘配置
# - server.availability_zone: 可用区

# 使用 JSON 模板创建
hcloud ECS CreateServers --cli-jsonInput=file://create_ecs.json --cli-region=cn-north-4
```

### 删除服务器

```bash
# 删除服务器（释放公网 IP 和数据盘）
hcloud ECS DeleteServers \
  --cli-jsonInput='{"servers":[{"id":"<server-id>"}],"delete_publicip":true,"delete_volume":true}' \
  --cli-region=cn-north-4
```

### 变更规格

```bash
# 变更规格（需先关机）
hcloud ECS ResizeServer \
  --server_id=<server-id> \
  --cli-jsonInput='{"resize":{"flavorRef":"<新规格ID>"}}' \
  --cli-region=cn-north-4

# 确认变更
hcloud ECS ConfirmServerResize \
  --server_id=<server-id> \
  --cli-region=cn-north-4
```

### 常见模式

**查询服务器的私网 IP：**
```bash
hcloud ECS ListServersDetails \
  --cli-query="servers[].{Name:name,IP:addresses.*[0].addr}" \
  --cli-region=cn-north-4
```

**查询关机状态的服务器：**
```bash
hcloud ECS ListServersDetails \
  --status=SHUTOFF \
  --cli-query="servers[].{ID:id,Name:name}" \
  --cli-region=cn-north-4
```

### 最佳实践
- 创建 ECS 前先确认 VPC、子网、安全组已就绪
- 生产环境使用多可用区部署
- 系统盘推荐 SSD 类型
- 配合弹性伸缩组使用实现自动扩缩

---

## BMS 裸金属服务器

裸金属服务器提供专属物理服务器，兼具虚拟化与物理机的优势。

### 常用操作

```bash
# 查询裸金属服务器列表
hcloud BMS ListBareMetalServers --cli-region=cn-north-4

# 查看详情
hcloud BMS ShowBaremetalServer --server_id=<server-id> --cli-region=cn-north-4

# 启动裸金属服务器
hcloud BMS BatchStartBaremetalServers \
  --cli-jsonInput='{"os-start":{"servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4

# 停止裸金属服务器
hcloud BMS BatchStopBaremetalServers \
  --cli-jsonInput='{"os-stop":{"servers":[{"id":"<server-id>"}]}}' \
  --cli-region=cn-north-4
```

---

## AS 弹性伸缩

弹性伸缩 (Auto Scaling) 根据业务需求和预设策略自动调整计算资源。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询伸缩组 | `ListScalingGroups` | 列出所有伸缩组 |
| 查询伸缩配置 | `ListScalingConfigs` | 列出伸缩配置 |
| 查询伸缩策略 | `ListScalingPolicies` | 列出某组的策略 |
| 查询伸缩活动 | `ListScalingActivityLogs` | 查看伸缩活动日志 |
| 创建伸缩组 | `CreateScalingGroup` | 新建伸缩组 |
| 启用/停用伸缩组 | `EnableOrDisableScalingGroup` | 控制伸缩组状态 |
| 执行伸缩策略 | `ExecuteScalingPolicy` | 手动触发伸缩 |

### 操作示例

```bash
# 查询所有伸缩组
hcloud AS ListScalingGroups --cli-region=cn-north-4

# 查看伸缩组详情
hcloud AS ShowScalingGroup --scaling_group_id=<group-id> --cli-region=cn-north-4

# 查看伸缩活动日志
hcloud AS ListScalingActivityLogs --scaling_group_id=<group-id> --cli-region=cn-north-4

# 查看伸缩组内实例
hcloud AS ListScalingInstances --scaling_group_id=<group-id> --cli-region=cn-north-4

# 手动执行伸缩策略
hcloud AS ExecuteScalingPolicy --scaling_policy_id=<policy-id> --cli-region=cn-north-4

# 启用伸缩组
hcloud AS EnableOrDisableScalingGroup \
  --scaling_group_id=<group-id> \
  --cli-jsonInput='{"action":"resume"}' \
  --cli-region=cn-north-4
```

---

## FunctionGraph 函数工作流

FunctionGraph 是华为云的 Serverless 计算服务，按需执行代码片段。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询函数列表 | `ListFunctions` | 列出所有函数 |
| 查询函数详情 | `ShowFunction` | 查看函数配置 |
| 创建函数 | `CreateFunction` | 新建函数 |
| 更新函数代码 | `UpdateFunctionCode` | 部署新代码 |
| 更新函数配置 | `UpdateFunctionConfig` | 修改函数配置 |
| 同步调用 | `InvokeFunction` | 同步执行函数 |
| 异步调用 | `AsyncInvokeFunction` | 异步执行函数 |
| 查询触发器 | `ListFunctionTriggers` | 列出函数触发器 |
| 删除函数 | `DeleteFunction` | 删除指定函数 |

### 操作示例

```bash
# 列出所有函数
hcloud FunctionGraph ListFunctions --cli-region=cn-north-4

# 查看函数详情
hcloud FunctionGraph ShowFunction \
  --function_urn=<function-urn> \
  --cli-region=cn-north-4

# 同步调用函数（带事件体）
hcloud FunctionGraph InvokeFunction \
  --function_urn=<function-urn> \
  --cli-jsonInput='{"key":"value"}' \
  --cli-region=cn-north-4

# 查看函数执行日志
hcloud FunctionGraph ListFunctionStatistics \
  --func_urn=<function-urn> \
  --period=300 \
  --cli-region=cn-north-4

# 创建函数（推荐使用 skeleton）
hcloud FunctionGraph CreateFunction --skeleton > create_func.json
# 编辑后执行
hcloud FunctionGraph CreateFunction --cli-jsonInput=file://create_func.json --cli-region=cn-north-4
```

### 最佳实践
- 在代码中避免长时间阻塞操作
- 设置合理的超时时间（默认 3 秒可能不够）
- 使用环境变量管理配置，避免代码硬编码
- 配合 API 网关或 OBS 触发器构建事件驱动架构
