# 网络服务参考

## 目录
- [VPC 虚拟私有云](#vpc-虚拟私有云)
- [ELB 弹性负载均衡](#elb-弹性负载均衡)
- [NAT 网关](#nat-网关)
- [EIP 弹性公网 IP](#eip-弹性公网-ip)
- [DNS 云解析服务](#dns-云解析服务)

---

## VPC 虚拟私有云

VPC 是云上的隔离网络环境。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询 VPC 列表 | `ListVpcs` | 获取所有 VPC (默认 v3) |
| 查询子网列表 | `ListSubnets` | 获取子网详情 |
| 查询策略组 | `ListSecurityGroups` | 获取安全组列表 |
| 查询规则 | `ListSecurityGroupRules` | 获取安全组规则 |
| 创建 VPC | `CreateVpc` | 新建虚拟私有云 |
| 修改 VPC | `UpdateVpc` | 变更名称或 CIDR |
| 删除 VPC | `DeleteVpc` | 删除 VPC |

### 分页与统计 (Agent 技巧)

> [!CAUTION]
> **v3 接口参数索引**：VPC v3 接口（如查询安全组规则）对于某些 ID 参数采用数组形式。
> **错误示例**：`--security_group_id=<id>` (会报错 Invalid parameter)
> **正确示例**：`--security_group_id.1=<id>` (必须带 `.1`)

```bash
# 智能统计 VPC 数量
hcloud VPC ListVpcs --cli-query="count"

# 获取安全组规则全量 (使用 .1 索引参数)
hcloud VPC ListSecurityGroupRules --security_group_id.1=<sg-id> --limit=100
```

### 常用命令示例

```bash
# 过滤指定名称的安全组 (建议使用 JMESPath，因为部分 v3 接口不支持 --name)
hcloud VPC ListSecurityGroups --cli-query="security_groups[?name=='default'].id"

# 查询子网关联的路由表
hcloud VPC ShowSubnet --subnet_id=<id> --cli-query="subnet.neutron_subnet_id"
```

---

## ELB 弹性负载均衡

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询负载均衡器 | `ListLoadBalancers` | 获取所有 ELB 实例 |
| 查询监听器 | `ListListeners` | 获取后端监听配置 |
| 查询后端服务器组 | `ListPools` | 获取 Pool 列表 |
| 查询后端服务器 | `ListMembers` | 查看组内云服务器 |

```bash
# 查看 ELB 状态及 IP
hcloud ELB ListLoadBalancers --cli-query="loadbalancers[].{Name:name, VIP:vip_address, Status:provisioning_status}"
```

---

## NAT 网关

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询 NAT 网关 | `ListNatGateways` | 获取所有 NAT 实例 |
| 查询 SNAT 规则 | `ListSnatRules` | 查看出口规则 |
| 查询 DNAT 规则 | `ListDnatRules` | 查看端口转发规则 |

---

## EIP 弹性公网 IP

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询 EIP 列表 | `ListPublicips/v3` | 获取所有弹性公网 IP |
| 申请 EIP | `CreatePublicip` | 购买新的 EIP |
| 绑定 EIP | `AssociatePublicips` | 将 EIP 绑定至资源 |
| 解绑 EIP | `DisassociatePublicips` | 解绑公网 IP |

---

## DNS 云解析服务

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询公网域名 | `ListPublicZones` | 列出所有托管域名 |
| 查询内网域名 | `ListPrivateZones` | 列出内网解析域名 |
| 查询解析记录 | `ListRecordSets` | 查看域名下的所有记录 |
| 创建记录 | `CreateRecordSet` | 新增 A/CNAME 等记录 |
