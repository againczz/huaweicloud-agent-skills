---
name: huaweicloud-container
description: 华为云容器服务 (CCE/SWR)。当用户提到云容器引擎、CCE、Kubernetes、K8s、集群、节点、节点池、kubeconfig、容器镜像服务、SWR、Docker镜像、镜像仓库、镜像推送拉取时触发。
---

# 华为云容器服务

通过 KooCLI (`hcloud`) 管理华为云容器资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- 命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`
- CCE 集群管理后可配合 `kubectl` 使用

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | List*, Show*, Get* | 只读查询，可直接执行 |
| 🟡 注意 | Create*, Update* | 会创建/修改资源 |
| 🔴 危险 | Delete* | 不可逆操作，务必确认 |

