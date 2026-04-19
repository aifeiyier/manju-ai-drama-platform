# Team Creator

> SOLO 适配文档 | 原始项目：team-creator | 版本 1.0.0

---

## 技能概述

| 项目 | 内容 |
|------|------|
| **技能名称** | Team Creator |
| **原始来源** | manju/team-creator |
| **核心定位** | 自动创建 Agent 团队，支持预设模板和自定义配置 |
| **适用平台** | SOLO Agent 环境 |

---

## 触发词

当用户提到以下关键词时，应激活此技能：

| 类别 | 触发词 |
|------|--------|
| 团队创建 | 创建团队、团队配置、Agent 团队、搭建团队 |
| 项目团队 | 项目团队、开发团队、AI团队 |
| 角色配置 | pm、ui、coding、tester、devops |
| 团队管理 | 团队协作、多agent、agent分工 |

---

## 5种预设模板

| 模板 | Agent 组成 | 适用场景 |
|------|------------|----------|
| `default` | pm + ui + coding | 标准项目团队（最常用） |
| `full` | pm + ui + coding + tester + devops | 完整职能团队 |
| `minimal` | pm + coding | 最小团队 |
| `ai` | pm + coding + researcher | AI/研究项目 |
| `web` | pm + ui + coding + devops | Web 项目 |

---

## 核心能力

| 能力 | 说明 |
|------|------|
| 智能需求分析 | 根据用户描述自动推荐最合适的模板 |
| 模板匹配 | 5种预设模板覆盖常见场景 |
| 团队创建 | 按模板一键创建多个专业 agent |
| 文件初始化 | 自动生成 IDENTITY.md、SOUL.md、AGENTS.md、USER.md |
| 共享目录 | 创建 buffer/ 共享工作区供协作 |
| 预览模式 | dry-run 模式先预览不实际创建 |
| 校验 | 创建后自动验证 agent 是否就绪 |
| 模型配置 | 支持全局模型和按 agent 类型指定不同模型 |

---

## 使用方法

### 智能推荐模板

```
输入需求描述，自动推荐最合适的模板
→ "我想做一个AI研究项目团队" → 推荐 ai 模板
→ "快速验证一个想法" → 推荐 minimal 模板
→ "开发一个Web应用" → 推荐 web 或 default 模板
```

### 查看可用模板

```
列出所有预设模板及其 Agent 组成
```

### 创建团队

| 参数 | 必填 | 说明 |
|------|------|------|
| action | 是 | `create` |
| template | 否 | 模板：default/full/minimal/ai/web |
| agents | 否 | 自定义 agent 列表（逗号分隔） |
| initFiles | 否 | 是否初始化文件（强烈推荐 true） |
| dryRun | 否 | 预览模式，不实际创建 |
| model | 否 | 全局模型 |
| force | 否 | 强制覆盖已存在 workspace |
| bindFeishu | 否 | 绑定飞书机器人 |

### 校验团队

| 参数 | 必填 | 说明 |
|------|------|------|
| action | 是 | `validate` |
| template | 是 | 模板名称 |

---

## 响应格式

### 创建成功响应

```json
{
  "success": true,
  "action": "create",
  "template": "default",
  "results": [
    { "name": "pm", "status": "ok", "workspace": "/path/to/workspace-pm" },
    { "name": "ui", "status": "ok", "workspace": "/path/to/workspace-ui" },
    { "name": "coding", "status": "ok", "workspace": "/path/to/workspace-coding" }
  ],
  "summary": {
    "created": 3,
    "skipped": 0,
    "dryRun": 0
  }
}
```

---

## 快速工作流

### 模板1：快速验证项目（Minimal）

```
1. 分析需求 → "快速验证一个想法，做个简单原型"
2. 创建最小团队 → minimal 模板（pm + coding）
3. 验证团队 → 确认所有 agent 就绪
4. 开始工作 → 给 pm 分配任务
```

### 模板2：标准软件项目（Default）

```
1. 分析需求 → "开发一个Web应用，需要UI设计和前后端开发"
2. 创建标准团队 → default 模板（pm + ui + coding）
3. 验证团队 → 确认所有 agent 就绪
4. 开始工作 → pm 制定计划，ui 设计界面，coding 实现功能
```

### 模板3：AI项目（AI）

```
1. 分析需求 → "做一个AI视频生成项目"
2. 创建AI团队 → ai 模板（pm + coding + researcher）
3. 验证团队 → 确认所有 agent 就绪
4. 开始工作 → pm 制定计划，researcher 研究方案，coding 实现
```

---

## 模板推荐逻辑

| 项目特征 | 推荐模板 | 理由 |
|---------|---------|------|
| 个人小项目，快速验证 | minimal | 只有 pm+coding，轻量高效 |
| 标准软件项目 | default | pm+ui+coding，完整分工 |
| 大型复杂项目 | full | pm+designer+frontend+backend+qa+sre |
| AI相关项目 | ai | pm+ml-engineer+data-scientist |
| Web应用项目 | web | pm+frontend+backend+devops |

---

## 反模式清单

### 绝对不要这样做

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 不确认需求直接创建团队 | 创建的团队可能不符合实际需要 | 先分析用户需求，推荐最合适的模板 |
| 所有项目都用 full 模板 | 过度设计，浪费资源 | 根据项目规模选择合适的模板 |
| 创建后不初始化 SOUL.md | agent 没有自己的身份和价值观 | 使用 initFiles: true 自动生成 |
| 不配置跨 agent 通信权限 | agent 之间无法协作 | 先配置 sessions 权限，再创建团队 |
| 不检查 workspace 冲突 | 可能覆盖已有 agent 的工作 | 创建前先检查是否已存在 |
| 创建团队后立即派任务 | agent 还没准备好 | 创建后先让 agent 了解协作规范 |
| 所有 agent 用同一个模型 | 不同角色需要不同能力 | pm/ui 用标准模型，coding 用更强的代码模型 |
| 跳过 dry-run 预览 | 可能创建不符合预期的团队 | 首次创建先用 dryRun: true 预览 |

### 常见错误

1. 不要在生产环境用 --force 强制覆盖
2. 不要忘记配置跨 agent 通信权限
3. 不要忽略 agent 的初始化文件
4. 不要在团队创建后立即删除 workspace
5. 不要忘记创建后验证团队是否正常工作

---

## 约束与校准

### 必须做的（Hard Constraints）
- 创建前必须先分析需求
- 首次创建必须用 dryRun 预览
- 必须用 initFiles: true 初始化 SOUL.md/AGENTS.md
- 创建前必须配置跨 agent 通信权限
- 创建后必须验证团队是否正常工作

### 推荐做的（Soft Constraints）
- 小项目优先用 minimal 模板
- 标准项目用 default 模板
- 按需绑定飞书（已有凭证时）
- 按 agent 类型配置不同模型
- 创建后给 agent 简单测试任务，验证协作正常

---

## 关键思维要点

1. 不要过度设计：小项目用 minimal，不要上来就 full
2. 先预览再创建：首次用 dryRun，确认无误再实际创建
3. initFiles 一定要开：SOUL.md/AGENTS.md 是协作基础
4. 先配置再创建：跨 agent 通信需要先配置权限
5. 创建后先测试：给 agent 简单任务，验证协作正常再开工
6. 不要随意强制覆盖：--force 会删除已有 agent 工作
7. 按角色分配任务：pm 管计划，ui 管设计，coding 管实现
8. 定期同步进度：避免 agent 之间信息不对称
