# Huawei Cloud Agent Skills

为 AI 编程助手（Antigravity / Claude Code 等）提供华为云 KooCLI (`hcloud`) 资源管理技能，覆盖 20+ 华为云核心服务。

## 功能概览

通过自然语言驱动 `hcloud` 命令，直接管理华为云资源：

- 🖥️ **计算**：ECS 弹性云服务器、BMS 裸金属、AS 弹性伸缩、FunctionGraph 函数计算
- 🌐 **网络**：VPC、ELB 负载均衡、NAT 网关、EIP 弹性公网 IP、DNS 域名解析
- 💾 **存储**：OBS 对象存储、EVS 云硬盘、SFS 弹性文件服务
- 🗄️ **数据库**：RDS（MySQL/PostgreSQL）、DDS（MongoDB）、DCS（Redis）、GaussDB
- 🐳 **容器**：CCE 云容器引擎、SWR 容器镜像服务
- 🔐 **安全**：IAM 统一身份认证
- 📊 **监控**：CES 云监控、CTS 云审计、SMN 消息通知
- 📨 **中间件**：DMS（Kafka/RabbitMQ）、CSS（Elasticsearch）

## 前提条件

1. 已安装 [KooCLI](https://support.huaweicloud.com/usermanual-hcli/hcli_02.html)
2. 已完成初始化配置：
   ```bash
   hcloud configure init
   ```

## 项目结构

```
huaweicloud-agent-skills/
├── SKILL.md                    # 主入口：命令格式、通用技巧、安全准则、服务路由
├── references/
│   ├── compute.md              # ECS / BMS / AS / FunctionGraph
│   ├── networking.md           # VPC / ELB / NAT / EIP / DNS
│   ├── storage.md              # OBS / EVS / SFS
│   ├── database.md             # RDS / DDS / DCS / GaussDB
│   ├── container.md            # CCE / SWR
│   ├── security.md             # IAM
│   ├── monitoring.md           # CES / CTS / SMN
│   └── middleware.md           # DMS (Kafka/RabbitMQ) / CSS (Elasticsearch)
├── README.md
├── LICENSE
└── .gitignore
```

## 安装

### 方式一：符号链接（开发模式）

```bash
ln -s /path/to/huaweicloud-agent-skills ~/.agents/skills/huaweicloud
```

### 方式二：直接复制

```bash
cp -r /path/to/huaweicloud-agent-skills ~/.agents/skills/huaweicloud
```

## 使用示例

安装后，在对话中直接用自然语言操作华为云资源：

```
# 查询
"帮我看一下 cn-north-4 的 ECS 列表"
"查询一下我们的 VPC 和子网"
"看看 RDS 实例的状态"

# 创建
"创建一个安全组，开放 80 和 443 端口"
"申请一个 5M 带宽的弹性公网 IP"

# 运维
"查一下这台 ECS 最近一小时的 CPU 使用率"
"帮我给 Kafka 集群新增一个 topic，3 分区 3 副本"

# 排查
"这个 ECS 为什么连不上？帮我看看安全组规则"
"RDS 慢查询日志看一下最近有没有问题"
```

## 设计理念

参考 [aws-agent-skills](https://github.com/itsmostafa/aws-agent-skills) 的架构：

- **SKILL.md** 作为核心入口，包含命令格式、通用技巧、安全准则和服务速查表
- **references/** 按服务类别组织详细的命令参考，agent 按需加载相关文件
- 所有知识本地化存储，无需实时联网查询文档，token 高效
- 操作按安全等级分类（🟢查询 / 🟡变更 / 🔴删除），变更类操作需确认

## 贡献

1. Fork 本仓库
2. 创建功能分支
3. 新增或更新服务参考文件
4. 提交 Pull Request

### 添加新服务的模板

在 `references/` 下对应的分类文件中追加，或新建文件：

```markdown
## 服务名称

简要说明。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询列表 | `ListXxx` | ... |

### 操作示例

\```bash
hcloud <Service> <Operation> --cli-region=cn-north-4
\```

### 最佳实践
- ...
```

## License

MIT License - see [LICENSE](LICENSE) for details.
