# 容器服务参考

## 目录
- [CCE 云容器引擎](#cce-云容器引擎)
- [SWR 容器镜像服务](#swr-容器镜像服务)

---

## CCE 云容器引擎

华为云高性能企业级 K8s 服务。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询集群列表 | `ListClusters` | 获取所有 CCE 集群 |
| 查询节点列表 | `ListNodes` | 获取集群内节点 |
| 查询节点池 | `ListNodePools` | 获取节点池详情 |
| 获取 kubeconfig | `ShowClusterEndpoints` | 获取证书以配置 kubectl |
| 创建集群 | `CreateCluster` | 创建 CCE 集群 |
| 删除集群 | `DeleteCluster` | 删除集群 |

### 分页与统计 (Agent 技巧)

```bash
# 智能统计集群数量
hcloud CCE ListClusters --cli-query="length(clusters)"

# 列出所有节点 (大集群务必检查分页)
hcloud CCE ListNodes --cluster_id=<id> --limit=100
```

---

## SWR 容器镜像服务

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询镜像仓库 | `ListReposDetails` | 获取镜像列表 |
| 查询组织 | `ListNamespaces` | 获取组织(Namespace)列表 |
| 查询镜像版本 | `ListRepositoryTags` | 查看 Tag 详情 |
| 创建组织 | `CreateNamespace` | 新建镜像组织 |
| 删除仓库 | `DeleteRepo` | 删除整个仓库 |

```bash
# 获取登录指令 (Docker Login)
hcloud SWR CreateAuthorizationToken --cli-query="authorization_token"
```

### Docker 操作 SWR 流程
1. 获取 Token: `hcloud SWR CreateAuthorizationToken`
2. 登录: `docker login -u cn-north-4@AK -p TOKEN swr.cn-north-4.myhuaweicloud.com`
3. 打标: `docker tag local_image swr.cn-north-4.myhuaweicloud.com/org/repo:tag`
4. 推送: `docker push swr.cn-north-4.myhuaweicloud.com/org/repo:tag`
