# 存储服务参考

## 目录
- [OBS 对象存储服务](#obs-对象存储服务)
- [EVS 云硬盘](#evs-云硬盘)
- [SFS 弹性文件服务](#sfs-弹性文件服务)

---

## OBS 对象存储服务

OBS (Object Storage Service) 使用 `hcloud obs` 命令集（基于 obsutil 指令）。

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 列出桶 | `hcloud obs ls` | 查看所有存储桶 |
| 列出对象 | `hcloud obs ls obs://bucket [-flat]` | 查看桶内文件 |
| 上传文件 | `hcloud obs cp local_file obs://bucket` | 上传文件或目录 |
| 下载文件 | `hcloud obs cp obs://bucket/file local_path` | 下载文件 |
| 同步目录 | `hcloud obs sync local_dir obs://bucket` | 增量同步 |
| 删除对象 | `hcloud obs rm obs://bucket/file [-r]` | 删除文件或文件夹 |
| 查看状态 | `hcloud obs stat obs://bucket/file` | 查看对象元数据 |

```bash
# 列出桶内前 100 个对象
hcloud obs ls obs://my-bucket -limit=100
```

---

## EVS 云硬盘

### 常用操作速查

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询硬盘列表 | `ListVolumes` | 获取所有云硬盘 |
| 查询硬盘详情 | `ShowVolume` | 查看特定硬盘信息 |
| 创建云硬盘 | `CreateVolume` | 购买新的硬盘 |
| 删除云硬盘 | `DeleteVolume` | 释放云硬盘 |
| 扩容硬盘 | `ResizeVolume` | 调大硬盘容量 |
| 查询快照 | `ListSnapshots` | 查看硬盘快照 |

```bash
# 智能统计 EVS 数量
hcloud EVS ListVolumes --cli-query="count"
```

---

## SFS 弹性文件服务

华为云提供 SFS Turbo 等多种文件存储。

### 常用操作速查 (SFS Turbo)

| 操作 | 命令 | 说明 |
|------|------|------|
| 查询文件系统 | `ListShares` | 获取所有 SFS Turbo 实例 |
| 查询详情 | `ShowShare` | 查看实例配置 |
| 创建文件系统 | `CreateShare` | 创建新的共享存储 |
| 扩容 | `ExpandShare` | 增加存储容量 |
| 查询权限规则 | `ListPermRules` | 查看挂载白名单 |

```bash
# 查询 SFS Turbo 实例 ID 及状态
hcloud SFSTurbo ListShares --cli-query="shares[].{ID:id, Status:status}"
```
