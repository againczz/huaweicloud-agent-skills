---
name: huaweicloud
description: 使用 KooCLI (hcloud) 管理华为云资源。覆盖 ECS、VPC、OBS、CCE、RDS、ELB、NAT、IAM、EVS、DNS、CES、FunctionGraph、SWR、AS、SMN 等 20+ 华为云服务。当用户提到华为云、hcloud、KooCLI、弹性云服务器、云容器、对象存储、负载均衡、云数据库、安全组、VPC、子网、云硬盘、云监控、弹性IP、NAT网关、函数计算、容器镜像等华为云相关资源操作时触发。即使用户未明确说"华为云"，只要涉及 hcloud 命令或华为云服务名称也应触发。
---

# 华为云 KooCLI 资源管理

通过华为云命令行工具 KooCLI（`hcloud`）管理华为云上的各类资源。KooCLI 对接华为云 API Explorer，支持调用所有已开放的华为云 API。

## 命令格式

所有 hcloud 命令遵循统一格式：

```
hcloud <Service> <Operation> [--参数名=值 ...]
```

**示例：**
```bash
# 查询 ECS 列表
hcloud ECS ListServersDetails

# 查询 VPC 列表
hcloud VPC ListVpcs

# 查看帮助
hcloud ECS ListServersDetails --help
```

## 通用技巧

### 帮助和发现

```bash
# 查看所有支持的服务
hcloud --help

# 查看某服务的所有操作
hcloud ECS --help

# 查看某操作的所有参数
hcloud ECS ListServersDetails --help

# 交互式模式（自动提示参数）
hcloud ECS ListServersDetails --interactive
```

### 输出控制

```bash
# 默认 JSON 输出
hcloud ECS ListServersDetails

# 表格输出
hcloud ECS ListServersDetails --cli-output=table

# JMESPath 过滤
hcloud ECS ListServersDetails --cli-query="servers[].{ID:id,Name:name,Status:status}"
```

### 调试和预检

```bash
# 调试模式（打印完整请求/响应）
hcloud ECS ListServersDetails --debug

# 干运行（仅校验，不实际执行）
hcloud ECS ListServersDetails --dryrun
```

### JSON 模板

对于参数较多的创建类操作，使用 skeleton 生成 JSON 模板：

```bash
# 生成参数模板
hcloud ECS CreateServers --skeleton > create_ecs.json

# 编辑 JSON 后执行
hcloud ECS CreateServers --cli-jsonInput=file://create_ecs.json
```

### 区域指定

```bash
# 命令中显式指定区域
hcloud ECS ListServersDetails --cli-region=cn-north-4

# 查看/修改默认配置
hcloud configure show
hcloud configure set --cli-region=cn-north-4
```

### 多配置管理

```bash
# 新增配置（生产/测试环境分离）
hcloud configure init --cli-profile=prod
hcloud configure init --cli-profile=dev

# 使用指定配置
hcloud ECS ListServersDetails --cli-profile=prod
```

## 安全准则

执行 hcloud 命令时遵循以下原则：

1. **查询类操作可直接执行**：List*、Show*、Get* 等只读操作安全性高
2. **变更类操作需谨慎**：Create*、Delete*、Update*、Batch* 等操作会修改资源
3. **删除操作需特别注意**：Delete* 操作不可逆，执行前确认目标资源的 ID/名称
4. **避免暴露凭据**：不在命令输出中打印 AK/SK，不在日志中记录敏感信息
5. **善用 `--dryrun`**：对不确定的变更操作先 dryrun 检查

### 操作安全等级

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Add* | 会创建/修改资源，注意参数正确性 |
| 🔴 危险 | Delete*, Batch*Delete*, Remove* | 不可逆操作，务必确认 |

## 服务速查

### 计算

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 弹性云服务器 | `ECS` | 云服务器实例管理 |
| 裸金属服务器 | `BMS` | 物理服务器管理 |
| 弹性伸缩 | `AS` | 自动扩缩容 |
| 函数工作流 | `FunctionGraph` | Serverless 函数计算 |

### 容器

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 云容器引擎 | `CCE` | Kubernetes 集群管理 |
| 容器镜像服务 | `SWR` | 容器镜像仓库 |

### 网络

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 虚拟私有云 | `VPC` | VPC/子网/安全组 |
| 弹性负载均衡 | `ELB` | 负载均衡器管理 |
| NAT 网关 | `NAT` | NAT 网关管理 |
| 弹性公网 IP | `EIP` | 公网 IP 管理 |
| 云解析服务 | `DNS` | DNS 域名解析 |

### 存储

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 对象存储服务 | `OBS` | 对象存储/桶管理 |
| 云硬盘 | `EVS` | 块存储卷管理 |
| 弹性文件服务 | `SFS` | 共享文件系统 |

### 数据库

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 关系型数据库 | `RDS` | MySQL/PostgreSQL/SQL Server |
| 文档数据库 | `DDS` | MongoDB 兼容 |
| 分布式缓存 | `DCS` | Redis/Memcached |
| GaussDB | `GaussDB` | 华为自研数据库 |

### 安全与身份

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 统一身份认证 | `IAM` | 用户/角色/权限管理 |

### 管理与监控

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 云监控 | `CES` | 指标监控/告警 |
| 云审计 | `CTS` | API 操作审计 |
| 消息通知 | `SMN` | 消息主题/订阅/发布 |
| 云日志 | `LTS` | 日志采集/查询/分析 |

### 应用中间件

| 服务 | hcloud 服务名 | 说明 |
|------|--------------|------|
| 分布式消息 | `DMS` | Kafka/RabbitMQ |
| 云搜索 | `CSS` | Elasticsearch |

## 按服务查阅详细参考

每个服务类别是一个独立的 skill，包含详细的命令示例和最佳实践：

| 类别 | Skill 路径 | 覆盖内容 |
|------|-----------|----------|
| 计算 | `skills/compute/` | ECS、BMS、AS、FunctionGraph |
| 网络 | `skills/networking/` | VPC、ELB、NAT、EIP、DNS |
| 存储 | `skills/storage/` | OBS、EVS、SFS |
| 数据库 | `skills/database/` | RDS、DDS、DCS、GaussDB |
| 容器 | `skills/container/` | CCE、SWR |
| 安全与身份 | `skills/security/` | IAM |
| 监控与运维 | `skills/monitoring/` | CES、CTS、SMN、LTS |
| 中间件 | `skills/middleware/` | DMS、CSS |

根据用户请求的服务类型，读取对应 skill 的 `SKILL.md` 和 `reference.md` 获取详细命令和模式。

## 故障排查通用步骤

### 命令执行失败

1. 添加 `--debug` 查看完整请求和响应
2. 检查错误码和错误信息
3. 确认 region 是否正确
4. 确认 AK/SK 是否有效
5. 确认 IAM 权限是否足够

### 常见错误码

| 错误码 | 含义 | 处理方式 |
|--------|------|----------|
| `APIGW.0301` | IAM 认证失败 | 检查 AK/SK 和 configure |
| `Ecs.0005` | ECS 配额不足 | 申请扩容配额 |
| `VPC.0101` | VPC 配额不足 | 申请扩容配额 |
| `404` | 资源不存在 | 确认资源 ID 和 region |

### 元数据更新

如果服务或操作找不到：

```bash
# 更新 KooCLI 元数据
hcloud meta download

# 确认不在离线模式
hcloud configure set --cli-offline=false
```

## 参考链接

- [KooCLI 官方文档](https://support.huaweicloud.com/usermanual-hcli/hcli_03.html)
- [华为云 API Explorer](https://console.huaweicloud.com/apiexplorer/)
- [华为云 CLI 示例](https://console.huaweicloud.com/apiexplorer/#/openapi)
