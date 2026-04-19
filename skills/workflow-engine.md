# 工作流引擎

> 版本：1.0.0
>
> 将多个技能串联成自动化工作流，支持变量引用和错误处理

---

## 触发词

`工作流` `自动化流程` `批量处理` `全链路` `任务串联` `任务流水线` `多步骤任务` `依次执行`

---

## 功能说明

工作流引擎将 AI 漫剧的多个技能模块串联成自动化工作流，实现从选题到出片的一键执行。

核心能力：
- **工作流定义** -- 使用 JSON 格式定义多步骤工作流
- **变量引用** -- 步骤间通过变量引用传递数据
- **错误处理** -- 可配置错误时是否立即停止
- **预置工作流** -- 3 个开箱即用的工作流模板

---

## 工作流定义格式

### 完整结构

```json
{
  "name": "工作流名称",
  "description": "工作流描述",
  "version": "1.0.0",
  "stopOnError": true,
  "config": {
    "project_name": "项目名称",
    "target_platform": "douyin",
    "duration_min": 60,
    "duration_max": 120,
    "aspect_ratio": "9:16",
    "resolution": "1080p",
    "auto_style_match": true,
    "dynamic_shots": true
  },
  "steps": [
    {
      "name": "步骤名称",
      "skill": "技能名称",
      "action": "操作名称",
      "params": {
        "参数名": "参数值"
      },
      "output_schema": "输出schema路径（可选）"
    }
  ]
}
```

### 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 工作流名称 |
| description | string | 否 | 工作流描述 |
| version | string | 否 | 版本号 |
| stopOnError | boolean | 否 | 遇到错误是否立即停止（默认 false） |
| config | object | 否 | 全局配置参数 |
| steps | array | 是 | 步骤列表 |
| steps[].name | string | 否 | 步骤名称（用于日志和展示） |
| steps[].skill | string | 是 | 技能名称 |
| steps[].action | string | 是 | 技能操作名称 |
| steps[].params | object | 否 | 传递给技能的参数 |
| steps[].output_schema | string | 否 | 输出数据校验的 schema 路径 |

---

## 变量引用机制

### 引用格式

变量引用使用 `${}` 语法：

| 引用类型 | 格式 | 示例 | 说明 |
|---------|------|------|------|
| 引用全局配置 | `${config.配置名}` | `${config.target_platform}` | 引用 config 中的参数 |
| 引用步骤输出 | `${steps.N.output}` | `${steps.0.output}` | 引用第 N 步的完整输出 |
| 引用嵌套字段 | `${steps.N.output.字段}` | `${steps.0.output.story_outline.title}` | 引用步骤输出的嵌套字段 |
| 引用步骤输出字段 | `${steps.N.output.字段.子字段}` | `${steps.1.output.storyboard}` | 深层嵌套引用 |

### 引用规则

1. 步骤编号从 0 开始
2. 只能引用已执行步骤的输出（不能向前引用）
3. 引用的字段不存在时，使用 null 作为默认值
4. config 引用在所有步骤执行前解析
5. steps 引用在每个步骤执行前解析

### 引用示例

```json
{
  "steps": [
    {
      "name": "选题分析",
      "skill": "topic-select",
      "action": "analyze",
      "params": {
        "creative_input": "一个仙帝在都市养老的故事",
        "target_platform": "${config.target_platform}",
        "auto_topic_detection": true
      }
    },
    {
      "name": "故事策划",
      "skill": "planning",
      "action": "create",
      "params": {
        "topic_analysis": "${steps.0.output}",
        "style_selection": "auto",
        "dynamic_shot_generation": "${config.dynamic_shots}",
        "target_duration": {
          "min": "${config.duration_min}",
          "max": "${config.duration_max}"
        }
      }
    },
    {
      "name": "素材生成",
      "skill": "production",
      "action": "generate",
      "params": {
        "storyboard": "${steps.1.output.storyboard}",
        "character_bible": "${steps.1.output.character_bible}",
        "scene_library": "${steps.1.output.scene_library}",
        "style_config": "${steps.1.output.style_config}",
        "consistency_check": true
      }
    },
    {
      "name": "剪辑合成",
      "skill": "editing",
      "action": "compile",
      "params": {
        "visual_assets": "${steps.2.output.visual_assets}",
        "audio_assets": "${steps.2.output.audio_assets}",
        "storyboard": "${steps.1.output.storyboard}",
        "auto_subtitles": true,
        "transition_effects": "auto",
        "quality": "high"
      }
    },
    {
      "name": "导出成片",
      "skill": "export",
      "action": "render",
      "params": {
        "final_cut": "${steps.3.output.final_cut}",
        "subtitle_file": "${steps.3.output.subtitle_file}",
        "target_platforms": ["douyin", "kuaishou", "xiaohongshu"],
        "metadata": {
          "title": "${steps.0.output.story_outline.title}",
          "description": "${steps.0.output.story_outline.logline}",
          "tags": ["AI漫剧", "${steps.0.output.topic_type}", "短视频"]
        }
      }
    }
  ]
}
```

---

## 预置工作流

### 1. complete（完整制作工作流）

**名称**：ai-manju-complete
**描述**：AI 漫剧完整制作工作流 - 从选题到出片的全链路自动化
**特点**：stopOnError = true，任何步骤失败立即停止

| 步骤 | 技能 | 操作 | 说明 |
|------|------|------|------|
| 1 | topic-select | analyze | 选题分析：题材识别、大纲生成 |
| 2 | planning | create | 故事策划：动态镜头生成、分镜设计 |
| 3 | production | generate | 素材生成：智能风格匹配、BGM 选择 |
| 4 | editing | compile | 剪辑合成：音画对齐、自动字幕 |
| 5 | export | render | 导出成片：多平台适配、质量校验 |

**全局配置**：
```json
{
  "project_name": "ai-manju-project",
  "target_platform": "douyin",
  "duration_min": 60,
  "duration_max": 120,
  "aspect_ratio": "9:16",
  "resolution": "1080p",
  "auto_style_match": true,
  "dynamic_shots": true
}
```

### 2. automated（全自动工作流）

**名称**：ai-manju-automated
**描述**：AI 漫剧全自动生产工作流 - 选题-策划-拍摄-剪辑-出片
**特点**：stopOnError = false，包含浏览器自动化视频生成步骤

| 步骤 | 技能 | 操作 | 说明 |
|------|------|------|------|
| 1 | topic-select | analyze | 选题分析：题材识别、大纲生成 |
| 2 | planning | generate-shots | 故事策划：动态镜头生成、分镜设计 |
| 3 | production | generate-assets | 素材生成：智能风格匹配、BGM 选择 |
| 4 | agent-browser | generate-videos | 浏览器自动化：通义万相视频生成 |
| 5 | editing | compose | 剪辑合成：音画对齐、自动字幕 |
| 6 | export | export-video | 导出成片：多平台适配、质量校验 |

### 3. production（制作工作流）

**名称**：ai-manju-production
**描述**：AI 漫剧制作全流程工作流 - 批量生成视频片段
**特点**：专注于视频生成和编译

| 步骤 | 技能 | 操作 | 说明 |
|------|------|------|------|
| 1 | gen-video | batch-generate | 批量生成视频片段 |
| 2 | gen-video | compile | 编译合成完整视频 |

---

## 错误处理策略

### stopOnError = true（严格模式）

- 任何步骤执行失败，立即停止整个工作流
- 适用于质量要求高的场景
- 已执行的步骤输出会被保留

### stopOnError = false（容错模式）

- 步骤执行失败不影响后续步骤
- 每个步骤的错误会被记录到错误日志
- 适用于批量处理场景
- 最终输出包含所有步骤的成功/失败状态

### 错误日志格式

```json
{
  "success": true,
  "outputs": [
    { "step": 0, "name": "选题分析", "status": "success", "output": { ... } },
    { "step": 1, "name": "故事策划", "status": "success", "output": { ... } },
    { "step": 2, "name": "素材生成", "status": "failed", "error": "..." }
  ],
  "errors": [
    { "step": 2, "name": "素材生成", "error": "错误详情" }
  ]
}
```

---

## 使用方法

### SOLO 执行步骤

1. 选择预置工作流或定义自定义工作流
2. 解析全局配置（config）
3. 按顺序执行每个步骤：
   a. 解析当前步骤的参数（替换变量引用）
   b. 读取对应技能文档
   c. 按技能文档的指南执行操作
   d. 收集步骤输出
   e. 如果 stopOnError = true 且执行失败，停止工作流
4. 汇总所有步骤的输出和错误
5. 输出工作流执行报告

### 自定义工作流

用户可以定义自己的工作流，只需指定步骤列表：

```json
{
  "name": "my-workflow",
  "steps": [
    { "skill": "topic-select", "action": "analyze", "params": { ... } },
    { "skill": "planning", "action": "create", "params": { ... } }
  ]
}
```

---

## 反模式

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 向前引用步骤输出 | 步骤 2 引用步骤 3 的输出，此时步骤 3 还未执行 | 只能引用已执行步骤（编号更小的步骤） |
| 变量引用路径错误 | `${steps.0.output.title}` 但实际字段是 `story_outline.title` | 确认输出结构，使用正确的嵌套路径 |
| 不设置 stopOnError | 默认容错模式可能掩盖严重错误 | 质量要求高的场景设为 true |
| 步骤间数据格式不匹配 | 上一步输出格式与下一步期望不符 | 按各技能文档的输出格式传递数据 |
| 工作流步骤过多 | 执行时间过长，出错概率增大 | 合理拆分，每个工作流控制在 5-7 步 |

---

_Powered by Dola - 赛博合伙人出品 - v1.0.0_
