---
name: huaweicloud-networking
description: 华为云网络服务 (VPC/ELB/NAT/EIP/DNS)。当用户提到虚拟私有云、VPC、子网、安全组、安全组规则、弹性负载均衡、ELB、NAT网关、弹性公网IP、EIP、DNS解析、域名记录、路由表、端口转发、SNAT、DNAT 时触发。
---

# 华为云网络服务

通过 KooCLI (`hcloud`) 管理华为云网络资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update*, Add* | 会创建/修改资源，注意参数正确性 |
| 🔴 危险 | Delete*, Batch*Delete* | 不可逆操作，务必确认 |

