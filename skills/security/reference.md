# 身份与权限参考 (IAM)

## 目录
- [用户管理](#用户管理)
- [用户组管理](#用户组管理)
- [权限与策略](#权限与策略)
- [访问密钥 (AK/SK)](#访问密钥-aksk)
- [项目管理](#项目管理)

---

## 用户管理

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询用户列表 | `KeystoneListUsers` | 获取所有 IAM 用户 |
| 查询用户详情 | `KeystoneShowUser` | 按 ID 或名称查询 |
| 创建用户 | `KeystoneCreateUser` | 新建 IAM 用户 |
| 修改用户 | `KeystoneUpdateUserByAdmin` | 修改用户信息 |
| 删除用户 | `KeystoneDeleteUser` | 删除用户 |

### 分页与统计 (Agent 技巧)

```bash
# 智能统计用户总数
hcloud IAM GetAccountSummaryV5 --cli-query="summary.users"

# 列出所有用户 (大型组织务必检查分页)
hcloud IAM KeystoneListUsers --limit=100
```

---

## 用户组管理

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询用户组 | `KeystoneListGroups` | 列出所有组 |
| 查询组内用户 | `KeystoneListUsersForGroupByAdmin` | 查看成员列表 |
| 用户入组 | `KeystoneAddUserToGroup` | 将用户加入组 |
| 用户出组 | `KeystoneRemoveUserFromGroup` | 将用户移出组 |

---

## 权限与策略

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询策略列表 | `ListPoliciesV5` | 获取系统及自定义策略 |
| 查询策略内容 | `GetPolicyV5` | 查看策略 JSON 定义 |
| 给组授权 | `KeystoneAssociateGroupWithProjectPermission` | 授予项目权限 |
| 查看授权 | `KeystoneListProjectPermissionsForGroup` | 查看组在该项目的权限 |

```bash
# 搜索特定关键词的策略
hcloud IAM ListPoliciesV5 --display_name=<keyword>
```

---

## 访问密钥 (AK/SK)

> [!WARNING]
> 绝不要在任何日志或公开地方记录 Secret Key (SK)。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 创建 AK/SK | `CreatePermanentAccessKey` | 为用户生成新密钥 |
| 查询密钥列表 | `ListAccessKeysV5` | 查看用户的 AK 列表 |
| 修改密钥状态 | `UpdateAccessKeyV5` | 启用或停用密钥 |
| 删除密钥 | `DeletePermanentAccessKey` | 彻底删除 API 密钥 |

---

## 项目管理

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询项目列表 | `KeystoneListProjects` | 获取所有项目 (Region) ID |
| 查询项目详情 | `KeystoneShowProject` | 查看项目属性 |
| 查询资源配额 | `ShowProjectQuota` | 查看该项目的资源上限 |

```bash
# 获取当前账号下所有项目的 ID 映射
hcloud IAM KeystoneListProjects --cli-query="projects[].{Name:name, ID:id}"
```
