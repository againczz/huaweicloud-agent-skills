# 存储服务参考

## 目录
- [OBS 对象存储服务](#obs-对象存储服务)
- [EVS 云硬盘](#evs-云硬盘)
- [SFS 弹性文件服务](#sfs-弹性文件服务)

---

## OBS 对象存储服务

对象存储服务 (Object Storage Service) 提供海量、安全、高可靠、低成本的数据存储。

KooCLI 已集成 obsutil 能力，可通过 `hcloud obs` 前缀直接操作 OBS。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 列出桶 | `hcloud obs ls` | 查看所有桶 |
| 列出对象 | `hcloud obs ls obs://<bucket>` | 查看桶内对象 |
| 上传文件 | `hcloud obs cp` | 上传本地文件到 OBS |
| 下载文件 | `hcloud obs cp` | 从 OBS 下载到本地 |
| 删除对象 | `hcloud obs rm` | 删除 OBS 对象 |
| 创建桶 | `hcloud obs mb` | 新建桶 |
| 删除桶 | `hcloud obs rb` | 删除桶 |

### 桶操作

```bash
# 列出所有桶
hcloud obs ls

# 创建桶
hcloud obs mb obs://my-bucket --location=cn-north-4

# 删除空桶
hcloud obs rb obs://my-bucket

# 强制删除非空桶（危险！会删除所有对象）
hcloud obs rb obs://my-bucket -f
```

### 对象操作

```bash
# 列出桶内对象
hcloud obs ls obs://my-bucket

# 列出指定前缀的对象
hcloud obs ls obs://my-bucket/path/to/

# 上传单个文件
hcloud obs cp /local/file.txt obs://my-bucket/file.txt

# 上传目录（递归）
hcloud obs cp /local/dir/ obs://my-bucket/dir/ -r

# 下载单个文件
hcloud obs cp obs://my-bucket/file.txt /local/file.txt

# 下载目录
hcloud obs cp obs://my-bucket/dir/ /local/dir/ -r

# 复制对象（桶间或桶内）
hcloud obs cp obs://src-bucket/file.txt obs://dst-bucket/file.txt

# 删除对象
hcloud obs rm obs://my-bucket/file.txt

# 批量删除
hcloud obs rm obs://my-bucket/dir/ -r

# 同步目录（只上传新增或变更的文件）
hcloud obs sync /local/dir/ obs://my-bucket/dir/
```

### 高级操作

```bash
# 大文件分段上传（自动触发）
hcloud obs cp /local/large-file.tar.gz obs://my-bucket/large-file.tar.gz \
  -ps=100 \  # 分段大小 100MB
  -p=5       # 5 个并发

# 查看对象元数据
hcloud obs stat obs://my-bucket/file.txt

# 设置对象 ACL
hcloud obs chmod obs://my-bucket/file.txt --acl=public-read
```

### 通过 API 操作 OBS

部分高级 OBS 管理功能需要通过标准 API 调用：

```bash
# 查询桶策略
hcloud OBS ShowBucketPolicy --bucket_name=my-bucket

# 设置桶的生命周期规则
hcloud OBS SetBucketLifecycle \
  --bucket_name=my-bucket \
  --skeleton > obs_lifecycle.json
# 编辑后执行
hcloud OBS SetBucketLifecycle \
  --bucket_name=my-bucket \
  --cli-jsonInput=file://obs_lifecycle.json
```

### 最佳实践
- 桶命名全局唯一，使用有意义的命名加前缀
- 敏感数据启用服务端加密
- 配合生命周期规则自动归档或清理过期数据
- 大文件上传使用分段上传提高可靠性

---

## EVS 云硬盘

云硬盘 (Elastic Volume Service) 为 ECS 提供块存储能力。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询云硬盘列表 | `ListVolumes` | 列出所有云硬盘 |
| 查询云硬盘详情 | `ShowVolume` | 查看单个云硬盘 |
| 创建云硬盘 | `CreateVolume` | 新建云硬盘 |
| 删除云硬盘 | `DeleteVolume` | 删除指定云硬盘 |
| 扩容云硬盘 | `ResizeVolume` | 扩大云硬盘容量 |
| 查询快照列表 | `ListSnapshots` | 列出所有快照 |
| 创建快照 | `CreateSnapshot` | 创建云硬盘快照 |

### 操作示例

```bash
# 列出所有云硬盘
hcloud EVS ListVolumes --cli-region=cn-north-4

# 精简输出
hcloud EVS ListVolumes \
  --cli-query="volumes[].{ID:id,Name:name,Size:size,Status:status}" \
  --cli-region=cn-north-4

# 查看详情
hcloud EVS ShowVolume --volume_id=<volume-id> --cli-region=cn-north-4

# 创建云硬盘（SSD，100G）
hcloud EVS CreateVolume \
  --cli-jsonInput='{
    "volume": {
      "name": "my-data-disk",
      "size": 100,
      "volume_type": "SSD",
      "availability_zone": "cn-north-4a"
    }
  }' \
  --cli-region=cn-north-4

# 扩容云硬盘
hcloud EVS ResizeVolume \
  --volume_id=<volume-id> \
  --cli-jsonInput='{"os-extend":{"new_size":200}}' \
  --cli-region=cn-north-4

# 创建快照
hcloud EVS CreateSnapshot \
  --cli-jsonInput='{
    "snapshot": {
      "name": "my-snapshot",
      "volume_id": "<volume-id>"
    }
  }' \
  --cli-region=cn-north-4

# 列出快照
hcloud EVS ListSnapshots --cli-region=cn-north-4

# 删除云硬盘
hcloud EVS DeleteVolume --volume_id=<volume-id> --cli-region=cn-north-4
```

### 云硬盘类型

| 类型 | volume_type | 适用场景 |
|------|------------|----------|
| 普通IO | `SATA` | 大容量、低成本、备份归档 |
| 高IO | `SAS` | 通用工作负载 |
| 超高IO | `SSD` | 高 IOPS 场景、数据库 |
| 极速IO | `GPSSD` | 超低延迟 |

---

## SFS 弹性文件服务

弹性文件服务 (Scalable File Service) 提供共享文件存储，支持 NFS 和 CIFS 协议。

### 常用操作

```bash
# 查询文件系统列表
hcloud SFS ListShares --cli-region=cn-north-4

# 查看详情
hcloud SFS ShowShare --share_id=<share-id> --cli-region=cn-north-4

# 创建文件系统
hcloud SFS CreateShare \
  --cli-jsonInput='{
    "share": {
      "name": "my-sfs",
      "share_proto": "NFS",
      "size": 100,
      "availability_zone": "cn-north-4a",
      "metadata": {
        "share_type": "default"
      }
    }
  }' \
  --cli-region=cn-north-4

# 扩容文件系统
hcloud SFS ExtendShare \
  --share_id=<share-id> \
  --cli-jsonInput='{"os-extend":{"new_size":200}}' \
  --cli-region=cn-north-4

# 删除文件系统
hcloud SFS DeleteShare --share_id=<share-id> --cli-region=cn-north-4
```
