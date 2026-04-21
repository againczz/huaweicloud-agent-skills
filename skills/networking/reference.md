# 网络服务参考

## 目录
- [VPC 虚拟私有云](#vpc-虚拟私有云)
- [ELB 弹性负载均衡](#elb-弹性负载均衡)
- [NAT 网关](#nat-网关)
- [EIP 弹性公网IP](#eip-弹性公网ip)
- [DNS 云解析服务](#dns-云解析服务)

---

## VPC 虚拟私有云

VPC 提供隔离的虚拟网络环境，包含子网、安全组、路由表等核心网络能力。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询 VPC 列表 | `ListVpcs` | 列出所有 VPC |
| 查询 VPC 详情 | `ShowVpc` | 查看单个 VPC |
| 创建 VPC | `CreateVpc` | 新建 VPC |
| 删除 VPC | `DeleteVpc` | 删除 VPC |
| 查询子网列表 | `ListSubnets` | 列出所有子网 |
| 创建子网 | `CreateSubnet` | 新建子网 |
| 查询安全组 | `ListSecurityGroups` | 列出安全组 |
| 查询安全组规则 | `ListSecurityGroupRules` | 列出安全组规则 |
| 创建安全组 | `CreateSecurityGroup` | 新建安全组 |
| 创建安全组规则 | `CreateSecurityGroupRule` | 添加安全组规则 |

### VPC 操作

```bash
# 列出所有 VPC
hcloud VPC ListVpcs --cli-region=cn-north-4

# 查看 VPC 详情
hcloud VPC ShowVpc --vpc_id=<vpc-id> --cli-region=cn-north-4

# 创建 VPC
hcloud VPC CreateVpc \
  --cli-jsonInput='{"vpc":{"name":"my-vpc","cidr":"192.168.0.0/16"}}' \
  --cli-region=cn-north-4

# 更新 VPC
hcloud VPC UpdateVpc \
  --vpc_id=<vpc-id> \
  --cli-jsonInput='{"vpc":{"name":"new-name"}}' \
  --cli-region=cn-north-4

# 删除 VPC（需先删除子网等子资源）
hcloud VPC DeleteVpc --vpc_id=<vpc-id> --cli-region=cn-north-4
```

### 子网操作

```bash
# 列出所有子网
hcloud VPC ListSubnets --cli-region=cn-north-4

# 按 VPC 过滤子网
hcloud VPC ListSubnets --vpc_id=<vpc-id> --cli-region=cn-north-4

# 查看子网详情
hcloud VPC ShowSubnet --subnet_id=<subnet-id> --cli-region=cn-north-4

# 创建子网
hcloud VPC CreateSubnet \
  --cli-jsonInput='{
    "subnet": {
      "name": "my-subnet",
      "cidr": "192.168.1.0/24",
      "gateway_ip": "192.168.1.1",
      "vpc_id": "<vpc-id>",
      "availability_zone": "cn-north-4a"
    }
  }' \
  --cli-region=cn-north-4

# 删除子网
hcloud VPC DeleteSubnet --vpc_id=<vpc-id> --subnet_id=<subnet-id> --cli-region=cn-north-4
```

### 安全组操作

```bash
# 列出所有安全组
hcloud VPC ListSecurityGroups --cli-region=cn-north-4

# 查看安全组规则
hcloud VPC ListSecurityGroupRules \
  --security_group_id=<sg-id> \
  --cli-region=cn-north-4

# 创建安全组
hcloud VPC CreateSecurityGroup \
  --cli-jsonInput='{"security_group":{"name":"my-sg","vpc_id":"<vpc-id>"}}' \
  --cli-region=cn-north-4

# 添加入站规则（允许 SSH）
hcloud VPC CreateSecurityGroupRule \
  --cli-jsonInput='{
    "security_group_rule": {
      "security_group_id": "<sg-id>",
      "direction": "ingress",
      "protocol": "tcp",
      "port_range_min": 22,
      "port_range_max": 22,
      "remote_ip_prefix": "0.0.0.0/0",
      "ethertype": "IPv4"
    }
  }' \
  --cli-region=cn-north-4

# 添加入站规则（允许 HTTP/HTTPS）
hcloud VPC CreateSecurityGroupRule \
  --cli-jsonInput='{
    "security_group_rule": {
      "security_group_id": "<sg-id>",
      "direction": "ingress",
      "protocol": "tcp",
      "port_range_min": 80,
      "port_range_max": 443,
      "remote_ip_prefix": "0.0.0.0/0",
      "ethertype": "IPv4"
    }
  }' \
  --cli-region=cn-north-4

# 删除安全组规则
hcloud VPC DeleteSecurityGroupRule --security_group_rule_id=<rule-id> --cli-region=cn-north-4
```

### 路由表操作

```bash
# 查询路由表
hcloud VPC ListRouteTables --cli-region=cn-north-4

# 查看路由表详情
hcloud VPC ShowRouteTable --routetable_id=<rt-id> --cli-region=cn-north-4
```

---

## ELB 弹性负载均衡

弹性负载均衡将访问流量分发到多个后端服务器，提高应用可用性。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询负载均衡器 | `ListLoadBalancers` | 列出所有 ELB |
| 查询监听器 | `ListListeners` | 列出监听器 |
| 查询后端组 | `ListPools` | 列出后端服务器组 |
| 查询后端成员 | `ListMembers` | 列出后端服务器 |
| 查询健康检查 | `ListHealthMonitors` | 列出健康检查 |
| 创建负载均衡器 | `CreateLoadBalancer` | 新建 ELB |
| 创建监听器 | `CreateListener` | 新建监听器 |
| 创建后端组 | `CreatePool` | 新建后端服务器组 |
| 添加后端成员 | `CreateMember` | 添加后端服务器 |

### 查询操作

```bash
# 列出所有负载均衡器
hcloud ELB ListLoadBalancers --cli-region=cn-north-4

# 查看 ELB 详情
hcloud ELB ShowLoadBalancer --loadbalancer_id=<lb-id> --cli-region=cn-north-4

# 列出监听器
hcloud ELB ListListeners --cli-region=cn-north-4

# 列出后端服务器组
hcloud ELB ListPools --cli-region=cn-north-4

# 查看后端成员
hcloud ELB ListMembers --pool_id=<pool-id> --cli-region=cn-north-4

# 查看健康检查配置
hcloud ELB ListHealthMonitors --cli-region=cn-north-4
```

### 创建 ELB 完整流程

```bash
# 1. 创建负载均衡器
hcloud ELB CreateLoadBalancer \
  --cli-jsonInput='{
    "loadbalancer": {
      "name": "my-elb",
      "vip_subnet_cidr_id": "<subnet-id>",
      "availability_zone_list": ["cn-north-4a"]
    }
  }' \
  --cli-region=cn-north-4

# 2. 创建监听器
hcloud ELB CreateListener \
  --cli-jsonInput='{
    "listener": {
      "name": "http-listener",
      "protocol": "HTTP",
      "protocol_port": 80,
      "loadbalancer_id": "<lb-id>"
    }
  }' \
  --cli-region=cn-north-4

# 3. 创建后端服务器组
hcloud ELB CreatePool \
  --cli-jsonInput='{
    "pool": {
      "name": "my-pool",
      "protocol": "HTTP",
      "lb_algorithm": "ROUND_ROBIN",
      "listener_id": "<listener-id>",
      "loadbalancer_id": "<lb-id>"
    }
  }' \
  --cli-region=cn-north-4

# 4. 添加后端服务器
hcloud ELB CreateMember \
  --pool_id=<pool-id> \
  --cli-jsonInput='{
    "member": {
      "address": "192.168.1.10",
      "protocol_port": 8080,
      "subnet_cidr_id": "<subnet-id>"
    }
  }' \
  --cli-region=cn-north-4

# 5. 配置健康检查
hcloud ELB CreateHealthMonitor \
  --cli-jsonInput='{
    "healthmonitor": {
      "pool_id": "<pool-id>",
      "type": "HTTP",
      "delay": 5,
      "timeout": 3,
      "max_retries": 3,
      "url_path": "/health"
    }
  }' \
  --cli-region=cn-north-4
```

---

## NAT 网关

NAT 网关为 VPC 内的私网服务器提供访问公网或被公网访问的能力。

### 常用操作

```bash
# 列出所有 NAT 网关
hcloud NAT ListNatGateways --cli-region=cn-north-4

# 查看 NAT 网关详情
hcloud NAT ShowNatGateway --nat_gateway_id=<nat-id> --cli-region=cn-north-4

# 列出 SNAT 规则
hcloud NAT ListSnatRules --cli-region=cn-north-4

# 列出 DNAT 规则
hcloud NAT ListDnatRules --cli-region=cn-north-4

# 创建 SNAT 规则（允许子网访问公网）
hcloud NAT CreateSnatRule \
  --cli-jsonInput='{
    "snat_rule": {
      "nat_gateway_id": "<nat-id>",
      "floating_ip_id": "<eip-id>",
      "subnet_id": "<subnet-id>"
    }
  }' \
  --cli-region=cn-north-4

# 创建 DNAT 规则（对外暴露内网服务）
hcloud NAT CreateDnatRule \
  --cli-jsonInput='{
    "dnat_rule": {
      "nat_gateway_id": "<nat-id>",
      "floating_ip_id": "<eip-id>",
      "private_ip": "192.168.1.10",
      "internal_service_port": 8080,
      "external_service_port": 80,
      "protocol": "tcp"
    }
  }' \
  --cli-region=cn-north-4
```

---

## EIP 弹性公网IP

弹性公网 IP 提供独立的公网 IP 地址，可绑定到 ECS、ELB、NAT 等资源。

### 常用操作

```bash
# 列出所有弹性公网 IP
hcloud EIP ListPublicips --cli-region=cn-north-4

# 查看 EIP 详情
hcloud EIP ShowPublicip --publicip_id=<eip-id> --cli-region=cn-north-4

# 申请弹性公网 IP
hcloud EIP CreatePublicip \
  --cli-jsonInput='{
    "publicip": {
      "type": "5_bgp"
    },
    "bandwidth": {
      "name": "my-bandwidth",
      "size": 5,
      "share_type": "PER",
      "charge_mode": "traffic"
    }
  }' \
  --cli-region=cn-north-4

# 绑定 EIP 到 ECS 端口
hcloud EIP UpdatePublicip \
  --publicip_id=<eip-id> \
  --cli-jsonInput='{"publicip":{"port_id":"<port-id>"}}' \
  --cli-region=cn-north-4

# 解绑 EIP
hcloud EIP UpdatePublicip \
  --publicip_id=<eip-id> \
  --cli-jsonInput='{"publicip":{"port_id":""}}' \
  --cli-region=cn-north-4

# 释放 EIP
hcloud EIP DeletePublicip --publicip_id=<eip-id> --cli-region=cn-north-4
```

---

## DNS 云解析服务

DNS 提供域名解析管理，支持公网和内网域名解析。

### 常用操作

```bash
# 列出所有 Zone
hcloud DNS ListPublicZones --cli-region=cn-north-4

# 查看 Zone 详情
hcloud DNS ShowPublicZone --zone_id=<zone-id> --cli-region=cn-north-4

# 列出记录集
hcloud DNS ListRecordSetsByZone --zone_id=<zone-id> --cli-region=cn-north-4

# 创建公网 Zone
hcloud DNS CreatePublicZone \
  --cli-jsonInput='{
    "name": "example.com.",
    "zone_type": "public"
  }' \
  --cli-region=cn-north-4

# 创建 A 记录
hcloud DNS CreateRecordSet \
  --zone_id=<zone-id> \
  --cli-jsonInput='{
    "name": "www.example.com.",
    "type": "A",
    "records": ["1.2.3.4"],
    "ttl": 300
  }' \
  --cli-region=cn-north-4

# 创建 CNAME 记录
hcloud DNS CreateRecordSet \
  --zone_id=<zone-id> \
  --cli-jsonInput='{
    "name": "cdn.example.com.",
    "type": "CNAME",
    "records": ["cdn.provider.com."],
    "ttl": 300
  }' \
  --cli-region=cn-north-4

# 删除记录集
hcloud DNS DeleteRecordSet \
  --zone_id=<zone-id> \
  --recordset_id=<recordset-id> \
  --cli-region=cn-north-4
```
