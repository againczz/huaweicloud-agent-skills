# 安全与身份服务参考

## IAM 统一身份认证

IAM (Identity and Access Management) 是华为云的身份认证和权限管理服务，控制谁可以访问哪些云资源。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询用户列表 | `KeystoneListUsers` | 列出所有 IAM 用户 |
| 查询用户详情 | `KeystoneShowUser` | 查看单个用户 |
| 创建用户 | `KeystoneCreateUser` | 新建 IAM 用户 |
| 删除用户 | `KeystoneDeleteUser` | 删除用户 |
| 查询用户组 | `KeystoneListGroups` | 列出用户组 |
| 创建用户组 | `KeystoneCreateGroup` | 新建用户组 |
| 查询项目列表 | `KeystoneListProjects` | 列出所有项目 |
| 查询角色列表 | `KeystoneListRoles` | 列出系统角色/策略 |
| 查询自定义策略 | `ListCustomPolicies` | 列出自定义策略 |
| 创建自定义策略 | `CreateCloudServiceCustomPolicy` | 新建自定义策略 |
| 查询 AK/SK | `ListPermanentAccessKeys` | 列出永久访问密钥 |
| 查询当前凭证 | `KeystoneShowSecurityCompliance` | 安全合规检查 |

### 用户管理

```bash
# 列出所有 IAM 用户
hcloud IAM KeystoneListUsers

# 精简输出
hcloud IAM KeystoneListUsers \
  --cli-query="users[].{ID:id,Name:name,Enabled:enabled,LastLogin:last_project_id}"

# 查看用户详情
hcloud IAM KeystoneShowUser --user_id=<user-id>

# 创建 IAM 用户
hcloud IAM KeystoneCreateUser \
  --cli-jsonInput='{
    "user": {
      "name": "new-user",
      "domain_id": "<domain-id>",
      "password": "<password>",
      "enabled": true,
      "description": "New team member"
    }
  }'

# 修改用户密码
hcloud IAM KeystoneUpdateUserPassword \
  --user_id=<user-id> \
  --cli-jsonInput='{
    "user": {
      "password": "<new-password>",
      "original_password": "<old-password>"
    }
  }'

# 删除用户
hcloud IAM KeystoneDeleteUser --user_id=<user-id>
```

### 用户组管理

```bash
# 列出用户组
hcloud IAM KeystoneListGroups

# 创建用户组
hcloud IAM KeystoneCreateGroup \
  --cli-jsonInput='{
    "group": {
      "name": "dev-team",
      "domain_id": "<domain-id>",
      "description": "Development team"
    }
  }'

# 将用户加入用户组
hcloud IAM KeystoneAddUserToGroup \
  --group_id=<group-id> \
  --user_id=<user-id>

# 查看用户组内的用户
hcloud IAM KeystoneListUsersForGroupByAdmin --group_id=<group-id>

# 将用户从用户组移除
hcloud IAM KeystoneRemoveUserFromGroup \
  --group_id=<group-id> \
  --user_id=<user-id>
```

### 权限与策略管理

```bash
# 列出所有系统权限/角色
hcloud IAM KeystoneListRoles

# 按名称过滤
hcloud IAM KeystoneListRoles \
  --cli-query="roles[?contains(display_name,'ECS')]"

# 列出自定义策略
hcloud IAM ListCustomPolicies

# 给用户组在项目上授权
hcloud IAM KeystoneAssociateGroupWithProjectPermission \
  --project_id=<project-id> \
  --group_id=<group-id> \
  --role_id=<role-id>

# 查看用户组在项目上的权限
hcloud IAM KeystoneListProjectPermissionsForGroup \
  --project_id=<project-id> \
  --group_id=<group-id>

# 取消授权
hcloud IAM KeystoneRemoveProjectPermissionFromGroup \
  --project_id=<project-id> \
  --group_id=<group-id> \
  --role_id=<role-id>
```

### 创建自定义策略

```bash
# 创建全局级自定义策略
hcloud IAM CreateCloudServiceCustomPolicy \
  --cli-jsonInput='{
    "role": {
      "display_name": "ECS-ReadOnly",
      "type": "XA",
      "description": "只读访问 ECS",
      "policy": {
        "Version": "1.1",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "ecs:servers:list",
              "ecs:servers:get"
            ]
          }
        ]
      }
    }
  }'

# 创建项目级自定义策略
hcloud IAM CreateCloudServiceCustomPolicy \
  --cli-jsonInput='{
    "role": {
      "display_name": "OBS-Upload",
      "type": "AX",
      "description": "仅允许上传到指定桶",
      "policy": {
        "Version": "1.1",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "obs:object:PutObject"
            ],
            "Resource": [
              "OBS:*:*:object:my-bucket/*"
            ]
          }
        ]
      }
    }
  }'
```

### 访问密钥管理

```bash
# 列出用户的永久访问密钥
hcloud IAM ListPermanentAccessKeys --user_id=<user-id>

# 创建永久访问密钥
hcloud IAM CreatePermanentAccessKey \
  --cli-jsonInput='{
    "credential": {
      "user_id": "<user-id>",
      "description": "For KooCLI"
    }
  }'

# 删除访问密钥
hcloud IAM DeletePermanentAccessKey --access_key=<ak>
```

### 项目管理

```bash
# 列出所有项目
hcloud IAM KeystoneListProjects

# 项目通常对应 Region，例如 cn-north-4 对应一个项目
# 精简输出
hcloud IAM KeystoneListProjects \
  --cli-query="projects[].{ID:id,Name:name,Enabled:enabled}"
```

### 安全最佳实践
- 不要使用主账号的 AK/SK 进行日常操作，创建 IAM 子用户
- 遵循最小权限原则，只授予必要的权限
- 定期轮换访问密钥
- 为敏感操作启用 MFA
- 使用用户组统一管理权限，避免直接给用户授权
- 使用自定义策略精细控制资源和操作范围

### 策略类型说明

| 类型 | type 值 | 说明 |
|------|---------|------|
| 全局级 | `XA` | 全局服务（如 IAM、OBS）的权限 |
| 项目级 | `AX` | 项目级服务（如 ECS、VPC）的权限 |

### 常见权限问题排查

1. **确认当前用户身份**：检查 `hcloud configure show` 查看当前 AK 对应的用户
2. **确认项目 ID**：不同 Region 有不同的 project_id，确保操作的资源和权限在同一项目下
3. **检查用户组权限**：用户的权限来自所属用户组，检查组的授权情况
4. **检查自定义策略**：确认 Statement 中的 Action 和 Resource 是否正确匹配
