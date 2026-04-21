---
name: huaweicloud-compute
description: 华为云计算服务 (ECS/BMS/AS/FunctionGraph)。当用户提到弹性云服务器、ECS、裸金属、BMS、弹性伸缩、AS、函数工作流、FunctionGraph、Serverless、云服务器创建删除启停、变更规格、查看实例列表时触发。
---

# 华为云计算服务

通过 KooCLI (`hcloud`) 管理华为云计算资源。

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

