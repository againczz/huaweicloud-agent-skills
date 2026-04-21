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

因为本项目为多模块集合，需分别链接每个子模块：

```bash
# 进入本地存放的 agents skills 目录
mkdir -p ~/.agents/skills && cd ~/.agents/skills

# 为项目的每个子模块创建软链
for skill in compute networking storage database container security monitoring middleware; do
  ln -s /path/to/huaweicloud-agent-skills/skills/$skill huaweicloud-$skill
done
```

## 命令格式与技巧

所有 hcloud 命令遵循统一格式：

```bash
hcloud <Service> <Operation> [--参数名=值 ...]
```

### 通用技巧

- **突破分页限制 (Agent 重点)**：华为云绝大多数查询接口（如 ECS、VPC 的 `List*`）**默认仅返回 25 条数据**。如果要统计总数或遍历全量资源，**务必在命令中加上大数值的分页参数**，例如 `--limit=1000`。
- **获取帮助**：`hcloud ECS --help` 或 `hcloud ECS ListServersDetails --help`
- **交互模式**：增加 `--interactive` 参数自动提示
- **干运行（预检）**：增加 `--dryrun` 测试操作边界而不实际执行
- **调试模式**：增加 `--debug` 查看底层 API 请求和响应报文
- **生成模板**：`hcloud ECS CreateServers --skeleton > create_ecs.json`
- **指定区域**：`--cli-region=cn-north-4` 
- **多配置组**：`--cli-profile=prod`

### 安全准则（Agent 需知）

- 🟢 **安全（List*, Show*, Get*）**：只读查询，可直接执行
- 🟡 **注意（Create*, Update*, Add*）**：会创建/修改资源，需二次校验参数
- 🔴 **危险（Delete*, Remove*, Batch*Delete*）**：不可逆操作，执行前必须确认目标资源 ID/名称


## 项目结构

```
huaweicloud-agent-skills/
├── .claude-plugin/
│   └── marketplace.json          # Claude Code 插件市场配置
├── skills/
│   ├── compute/
│   │   ├── SKILL.md              # 计算服务入口 (独立触发)
│   │   └── reference.md          # ECS / BMS / AS / FunctionGraph 详细参考
│   ├── networking/
│   │   ├── SKILL.md              # 网络服务入口
│   │   └── reference.md          # VPC / ELB / NAT / EIP / DNS 详细参考
│   ├── storage/
│   │   ├── SKILL.md              # 存储服务入口
│   │   └── reference.md          # OBS / EVS / SFS 详细参考
│   ├── database/
│   │   ├── SKILL.md              # 数据库服务入口
│   │   └── reference.md          # RDS / DDS / DCS / GaussDB 详细参考
│   ├── container/
│   │   ├── SKILL.md              # 容器服务入口
│   │   └── reference.md          # CCE / SWR 详细参考
│   ├── security/
│   │   ├── SKILL.md              # 安全身份入口
│   │   └── reference.md          # IAM 详细参考
│   ├── monitoring/
│   │   ├── SKILL.md              # 监控运维入口
│   │   └── reference.md          # CES / CTS / SMN / LTS 详细参考
│   └── middleware/
│       ├── SKILL.md              # 中间件入口
│       └── reference.md          # DMS / CSS 详细参考
├── README.md
├── LICENSE
└── .gitignore
```

### 插件化架构说明 (Monorepo)

本项目是一个 **Skill 集合（Monorepo）**，它不包含根级别的 `SKILL.md`。

每个 `skills/<category>/` 是一个**完全独立的 skill**，拥有自己的：

- **SKILL.md** — 包含 frontmatter（name + description 触发短语），Agent 根据触发短语精准匹配。
- **reference.md** — 补充文件，包含该类别的详细命令示例和最佳实践。

**优势：**
- ✅ **彻底解耦**：避免所有服务的指令堆砌在一起，Agent 脑容量更清晰。
- ✅ **按需加载**：支持用户只加载需要的服务模块（如只链接 `skills/compute`）。
- ✅ **精准触发**：每个 skill 独立定义触发短语，避免大一统匹配带来的噪音。

## 使用示例

安装后，在对话中直接用自然语言操作华为云资源：

```
# 查询
"帮我看一下 cn-north-4 的 ECS 列表"
"查询一下我们的 VPC 和子网"

# 创建
"创建一个安全组，开放 80 和 443 端口"

# 运维
"查一下这台 ECS 最近一小时的 CPU 使用率"

# 排查
"这个 ECS 为什么连不上？帮我看看安全组规则"
"RDS 慢查询日志看一下最近有没有问题"
```

## 常见错误排查

| 错误码 | 含义 | 处理方式 |
|--------|------|----------|
| `APIGW.0301` | IAM 认证失败 | 检查配置 `hcloud configure show` 或 AK/SK |
| `Ecs.0005` | ECS 配额不足 | 前往控制台申请扩容配额 |
| `VPC.0101` | VPC 配额不足 | 前往控制台申请扩容配额 |
| `404` | 资源不存在 | 确认资源 ID 是否正确，或 Region 是否匹配 |

如果出现 "Unsupported operation" 或服务找不到，请更新元数据：
```bash
hcloud meta download
hcloud configure set --cli-offline=false
```

## 贡献与添加新服务

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
