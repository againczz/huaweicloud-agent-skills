# 容器服务参考

## 目录
- [CCE 云容器引擎](#cce-云容器引擎)
- [SWR 容器镜像服务](#swr-容器镜像服务)

---

## CCE 云容器引擎

CCE (Cloud Container Engine) 提供托管的 Kubernetes 集群服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询集群列表 | `ListClusters` | 列出所有 CCE 集群 |
| 查询集群详情 | `ShowCluster` | 查看单个集群 |
| 创建集群 | `CreateCluster` | 新建 K8s 集群 |
| 删除集群 | `DeleteCluster` | 删除集群 |
| 查询节点列表 | `ListNodes` | 列出集群节点 |
| 创建节点 | `CreateNode` | 添加工作节点 |
| 删除节点 | `DeleteNode` | 移除工作节点 |
| 查询节点池 | `ListNodePools` | 列出节点池 |
| 创建节点池 | `CreateNodePool` | 创建节点池 |
| 获取 kubeconfig | `CreateKubernetesClusterCert` | 下载集群凭证 |

### 集群操作

```bash
# 列出所有 CCE 集群
hcloud CCE ListClusters --cli-region=cn-north-4

# 精简输出
hcloud CCE ListClusters \
  --cli-query="items[].{ID:metadata.uid,Name:metadata.name,Status:status.phase,Version:spec.version}" \
  --cli-region=cn-north-4

# 查看集群详情
hcloud CCE ShowCluster --cluster_id=<cluster-id> --cli-region=cn-north-4

# 获取 kubeconfig（用于 kubectl）
hcloud CCE CreateKubernetesClusterCert \
  --cluster_id=<cluster-id> \
  --cli-jsonInput='{"duration":-1}' \
  --cli-region=cn-north-4

# 创建集群（推荐使用 skeleton）
hcloud CCE CreateCluster --skeleton > create_cce.json
# 编辑 create_cce.json，主要填写：
# - metadata.name: 集群名称
# - spec.flavor: 集群规格（cce.s1.small/cce.s1.medium/cce.s2.large）
# - spec.type: VirtualMachine（虚拟机集群）
# - spec.hostNetwork.vpc: VPC ID
# - spec.hostNetwork.subnet: 子网 ID
# - spec.containerNetwork.mode: overlay_l2 或 vpc-router
# - spec.containerNetwork.cidr: 容器网段
hcloud CCE CreateCluster --cli-jsonInput=file://create_cce.json --cli-region=cn-north-4

# 删除集群
hcloud CCE DeleteCluster --cluster_id=<cluster-id> --cli-region=cn-north-4
```

### 节点操作

```bash
# 列出集群节点
hcloud CCE ListNodes --cluster_id=<cluster-id> --cli-region=cn-north-4

# 精简输出
hcloud CCE ListNodes \
  --cluster_id=<cluster-id> \
  --cli-query="items[].{ID:metadata.uid,Name:metadata.name,IP:status.privateIP,Status:status.phase}" \
  --cli-region=cn-north-4

# 查看节点详情
hcloud CCE ShowNode \
  --cluster_id=<cluster-id> \
  --node_id=<node-id> \
  --cli-region=cn-north-4

# 创建节点（推荐 skeleton）
hcloud CCE CreateNode --cluster_id=<cluster-id> --skeleton > create_node.json
hcloud CCE CreateNode \
  --cluster_id=<cluster-id> \
  --cli-jsonInput=file://create_node.json \
  --cli-region=cn-north-4

# 删除节点
hcloud CCE DeleteNode \
  --cluster_id=<cluster-id> \
  --node_id=<node-id> \
  --cli-region=cn-north-4
```

### 节点池操作

```bash
# 列出节点池
hcloud CCE ListNodePools --cluster_id=<cluster-id> --cli-region=cn-north-4

# 查看节点池详情
hcloud CCE ShowNodePool \
  --cluster_id=<cluster-id> \
  --nodepool_id=<nodepool-id> \
  --cli-region=cn-north-4

# 创建节点池
hcloud CCE CreateNodePool --cluster_id=<cluster-id> --skeleton > create_nodepool.json
hcloud CCE CreateNodePool \
  --cluster_id=<cluster-id> \
  --cli-jsonInput=file://create_nodepool.json \
  --cli-region=cn-north-4

# 更新节点池（扩缩容）
hcloud CCE UpdateNodePool \
  --cluster_id=<cluster-id> \
  --nodepool_id=<nodepool-id> \
  --cli-jsonInput='{
    "spec": {
      "initialNodeCount": 5
    }
  }' \
  --cli-region=cn-north-4

# 删除节点池
hcloud CCE DeleteNodePool \
  --cluster_id=<cluster-id> \
  --nodepool_id=<nodepool-id> \
  --cli-region=cn-north-4
```

### 集群规格

| 规格 | flavor | 适用场景 |
|------|--------|----------|
| 小规模 | `cce.s1.small` | 50 节点以内 |
| 中等规模 | `cce.s1.medium` | 200 节点以内 |
| 大规模 | `cce.s2.large` | 1000 节点以内 |
| 超大规模 | `cce.s2.xlarge` | 2000 节点以内 |

### 最佳实践
- 容器网络模式选择：普通规模用 overlay_l2，高性能用 vpc-router
- 生产环境至少使用 3 个 Master 节点的 HA 集群
- 使用节点池管理不同规格的工作节点
- 获取 kubeconfig 后可配合 kubectl 进行 K8s 原生管理

---

## SWR 容器镜像服务

SWR (SoftWare Repository for Container) 提供容器镜像的全生命周期管理。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询组织列表 | `ListNamespaces` | 列出镜像组织 |
| 查询仓库列表 | `ListRepositories` | 列出镜像仓库 |
| 查询镜像版本 | `ListRepositoryTags` | 列出镜像 Tag |
| 创建组织 | `CreateNamespace` | 新建组织 |
| 创建仓库 | `CreateRepository` | 新建仓库 |
| 删除仓库 | `DeleteRepository` | 删除仓库 |
| 删除镜像版本 | `DeleteRepositoryTag` | 删除指定 Tag |

### 操作示例

```bash
# 列出所有组织
hcloud SWR ListNamespaces --cli-region=cn-north-4

# 列出组织下的仓库
hcloud SWR ListRepositories --namespace=<org-name> --cli-region=cn-north-4

# 查看镜像版本
hcloud SWR ListRepositoryTags \
  --namespace=<org-name> \
  --repository=<repo-name> \
  --cli-region=cn-north-4

# 创建组织
hcloud SWR CreateNamespace \
  --cli-jsonInput='{"namespace":"my-org"}' \
  --cli-region=cn-north-4

# 创建仓库
hcloud SWR CreateRepository \
  --namespace=<org-name> \
  --cli-jsonInput='{
    "repository": "<repo-name>",
    "is_public": false,
    "category": "other",
    "description": "My container image"
  }' \
  --cli-region=cn-north-4

# 删除镜像版本
hcloud SWR DeleteRepositoryTag \
  --namespace=<org-name> \
  --repository=<repo-name> \
  --tag=v1.0.0 \
  --cli-region=cn-north-4

# 删除仓库
hcloud SWR DeleteRepository \
  --namespace=<org-name> \
  --repository=<repo-name> \
  --cli-region=cn-north-4
```

### Docker 登录 SWR

```bash
# 获取临时登录命令（需要 AK/SK 已配置）
# 登录 SWR 容器镜像仓库
docker login -u <region>@<ak> -p <login-token> swr.<region>.myhuaweicloud.com

# 推送镜像
docker tag my-image:v1 swr.cn-north-4.myhuaweicloud.com/<org>/<repo>:v1
docker push swr.cn-north-4.myhuaweicloud.com/<org>/<repo>:v1

# 拉取镜像
docker pull swr.cn-north-4.myhuaweicloud.com/<org>/<repo>:v1
```

### 最佳实践
- 每个项目或团队使用独立的组织 (namespace) 来隔离镜像
- 配合 CI/CD 流水线自动构建和推送镜像
- 设置镜像保留策略，自动清理过期版本
- 私有仓库搭配 imagePullSecret 在 CCE 中使用
