---
name: huaweicloud-storage
description: 华为云存储服务 (OBS/EVS/SFS)。注意：EVS/SFS 等 API 默认仅返回 25 条。若结果恰好为 25 条或 count 字段大于列表长度，应使用 --limit 或 --marker 获取全量。OBS 操作 (hcloud obs) 若对象极多，需注意分页浏览。
---

# 华为云存储服务

通过 KooCLI (`hcloud`) 管理华为云存储资源。

## 前提

- 已安装并配置 KooCLI：`hcloud configure init`
- OBS 操作使用 `hcloud obs` 前缀（集成 obsutil）
- 其他服务命令格式：`hcloud <Service> <Operation> [--参数=值]`
- 所有操作需指定区域：`--cli-region=cn-north-4`

## 安全准则

| 等级 | 操作前缀 | 说明 |
|------|----------|------|
| 🟢 安全 | ls, stat, List*, Show* | 只读查询，可直接执行 |
| 🟡 注意 | cp, sync, Create*, mb | 会创建/上传资源 |
| 🔴 危险 | rm -r, rb -f, Delete* | 不可逆删除，务必确认 |

