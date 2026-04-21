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
- 📊 **监控**：CES 云监控、CTS 云审计、SMN 消息通知、LTS 云日志
- 📨 **中间件**：DMS（Kafka/RabbitMQ）、CSS（Elasticsearch）

## 前提条件

1. 已安装 [KooCLI](https://support.huaweicloud.com/usermanual-hcli/hcli_02.html)
2. 已完成初始化配置：
   ```bash
   hcloud configure init
   ```

## 安装

### Claude Code

```bash
# 从插件市场
/plugin marketplace add againczz/huaweicloud-agent-skills
/plugin install huaweicloud-agent-skills

# 或直接从 GitHub
/plugin install https://github.com/againczz/huaweicloud-agent-skills
```

### Codex (OpenAI)

```bash
# 克隆到全局 skills 目录
git clone https://github.com/againczz/huaweicloud-agent-skills.git \
  ~/.codex/skills/huaweicloud

# 或克隆到项目级 skills 目录
git clone https://github.com/againczz/huaweicloud-agent-skills.git \
  .agents/skills/huaweicloud
```

### OpenCode

```bash
# 克隆到全局 skills 目录
git clone https://github.com/againczz/huaweicloud-agent-skills.git \
  ~/.config/opencode/skills/huaweicloud

# 或克隆到项目级 skills 目录
git clone https://github.com/againczz/huaweicloud-agent-skills.git \
  .opencode/skills/huaweicloud
```

### 本地开发（通用）

```bash
# 符号链接（推荐）
ln -s /path/to/huaweicloud-agent-skills ~/.agents/skills/huaweicloud

# 或直接复制
cp -r /path/to/huaweicloud-agent-skills ~/.agents/skills/huaweicloud
```

## 项目结构

```
huaweicloud-agent-skills/
├── .claude-plugin/
│   └── marketplace.json          # Claude Code 插件市场配置
├── SKILL.md                      # 根入口：命令格式、通用技巧、安全准则、服务路由
├── skills/
│   ├── compute/
│   │   ├── SKILL.md              # 计算服务入口 (独立触发)
│   │   └── reference.md          # ECS / BMS / AS / FunctionGraph 详细参考
│   ├── networking/
│   │   ├── SKILL.md              # 网络服务入口 (独立触发)
│   │   └── reference.md          # VPC / ELB / NAT / EIP / DNS 详细参考
│   ├── storage/
│   │   ├── SKILL.md              # 存储服务入口 (独立触发)
│   │   └── reference.md          # OBS / EVS / SFS 详细参考
│   ├── database/
│   │   ├── SKILL.md              # 数据库服务入口 (独立触发)
│   │   └── reference.md          # RDS / DDS / DCS / GaussDB 详细参考
│   ├── container/
│   │   ├── SKILL.md              # 容器服务入口 (独立触发)
│   │   └── reference.md          # CCE / SWR 详细参考
│   ├── security/
│   │   ├── SKILL.md              # 安全身份入口 (独立触发)
│   │   └── reference.md          # IAM 详细参考
│   ├── monitoring/
│   │   ├── SKILL.md              # 监控运维入口 (独立触发)
│   │   └── reference.md          # CES / CTS / SMN / LTS 详细参考
│   └── middleware/
│       ├── SKILL.md              # 中间件入口 (独立触发)
│       └── reference.md          # DMS / CSS 详细参考
├── README.md
├── LICENSE
└── .gitignore
```

### 插件化架构说明

每个 `skills/<category>/` 是一个**独立 skill**，拥有自己的：

- **SKILL.md** — 包含 frontmatter（name + description 触发短语），Agent 根据触发短语精准匹配
- **reference.md** — 补充文件，包含该类别的详细命令示例和最佳实践

**优势：**
- ✅ **按需加载**：Agent 只加载用户请求相关的 skill，节省 token
- ✅ **精准触发**：每个 skill 独立定义触发短语，避免笼统匹配
- ✅ **独立安装**：支持只安装需要的服务模块
- ✅ **插件市场**：通过 `.claude-plugin/marketplace.json` 注册全部 8 个 skill

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

参考 [aws-agent-skills](https://github.com/itsmostafa/aws-agent-skills) 的插件化架构：

- **根 SKILL.md** 作为全局入口，包含通用命令格式、安全准则和服务路由表
- **skills/** 下每个类别是独立 skill，有自己的触发描述和详细参考
- 所有知识本地化存储，无需实时联网查询文档，token 高效
- 操作按安全等级分类（🟢查询 / 🟡变更 / 🔴删除），变更类操作需确认

## 贡献

1. Fork 本仓库
2. 创建功能分支
3. 新增或更新 `skills/<category>/reference.md`
4. 提交 Pull Request

### 添加新服务类别

1. 创建目录 `skills/<new-category>/`
2. 创建 `SKILL.md`，包含 frontmatter：
   ```yaml
   ---
   name: huaweicloud-<new-category>
   description: 华为云xx服务。当用户提到...时触发。
   ---
   ```
3. 创建 `reference.md`，包含命令示例和最佳实践
4. 在 `.claude-plugin/marketplace.json` 的 skills 数组中添加路径

## License

MIT License - see [LICENSE](LICENSE) for details.
