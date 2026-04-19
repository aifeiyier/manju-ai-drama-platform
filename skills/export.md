# 导出成片模块

> 版本：1.0.0
>
> 多平台适配、质量校验、元数据自动生成

---

## 触发词

`导出` `发布` `多平台` `适配` `平台配置` `元数据` `标题截断` `hashtag` `成片导出`

---

## 功能说明

导出成片模块是 AI 漫剧创作链路的最后一步。接收剪辑合成的成片，执行多平台适配、质量校验和元数据生成，输出可直接发布到各平台的成片和配套元数据。

核心能力：
- **多平台适配** -- 为 4 个主流短视频平台生成适配版本
- **质量校验** -- 全面的视频/音频/字幕质量检查
- **元数据自动生成** -- 标题截断、标签限制、hashtag 自动生成
- **导出清单** -- 生成完整的导出 manifest 文件

---

## 4 个平台配置

### 抖音（douyin）

| 参数 | 值 |
|------|-----|
| 平台名称 | 抖音 |
| 分辨率 | 1080x1920 |
| 画面比例 | 9:16 |
| 最大时长 | 300 秒（5分钟） |
| 文件格式 | mp4 |
| 推荐码率 | 8Mbps |
| 帧率 | 30fps |
| 标题最大长度 | 55 字符 |
| 描述最大长度 | 500 字符 |
| 标签最大数量 | 10 个 |

### 快手（kuaishou）

| 参数 | 值 |
|------|-----|
| 平台名称 | 快手 |
| 分辨率 | 1080x1920 |
| 画面比例 | 9:16 |
| 最大时长 | 300 秒（5分钟） |
| 文件格式 | mp4 |
| 推荐码率 | 8Mbps |
| 帧率 | 30fps |
| 标题最大长度 | 80 字符 |
| 描述最大长度 | 800 字符 |
| 标签最大数量 | 15 个 |

### 小红书（xiaohongshu）

| 参数 | 值 |
|------|-----|
| 平台名称 | 小红书 |
| 分辨率 | 1080x1920 |
| 画面比例 | 9:16 |
| 最大时长 | 300 秒（5分钟） |
| 文件格式 | mp4 |
| 推荐码率 | 10Mbps |
| 帧率 | 30fps |
| 标题最大长度 | 20 字符 |
| 描述最大长度 | 1000 字符 |
| 标签最大数量 | 10 个 |

### 视频号（shipinhao）

| 参数 | 值 |
|------|-----|
| 平台名称 | 视频号 |
| 分辨率 | 1080x1920 |
| 画面比例 | 9:16 |
| 最大时长 | 1800 秒（30分钟） |
| 文件格式 | mp4 |
| 推荐码率 | 8Mbps |
| 帧率 | 30fps |
| 标题最大长度 | 30 字符 |
| 描述最大长度 | 300 字符 |
| 标签最大数量 | 10 个 |

### 平台对比

| 参数 | 抖音 | 快手 | 小红书 | 视频号 |
|------|------|------|--------|--------|
| 最大时长 | 300s | 300s | 300s | 1800s |
| 推荐码率 | 8Mbps | 8Mbps | 10Mbps | 8Mbps |
| 标题长度 | 55 | 80 | 20 | 30 |
| 描述长度 | 500 | 800 | 1000 | 300 |
| 标签数量 | 10 | 15 | 10 | 10 |

---

## 质量校验

### 校验项目

| 检查项 | 默认状态 | 说明 |
|--------|---------|------|
| 视频分辨率 | pass | 1080x1920 |
| 视频帧率 | pass | 30fps |
| 视频码率 | pass | 10Mbps |
| 音频采样率 | pass | 44.1kHz |
| 音频声道 | pass | 立体声 |
| 文件格式 | pass | MP4(H.264/AAC) |
| 字幕文件 | pass | SRT 格式 |

### 校验评分

- **基础分**：0.9
- **每个问题**：-0.05
- **问题 < 3 个**：合规检查通过（pass）
- **问题 >= 3 个**：合规检查失败（fail）
- **最终评分**：max(0.6, 基础分 - 问题数 * 0.05)

---

## 元数据自动生成

### 基础元数据

```json
{
  "title": "AI漫剧作品",
  "description": "这是一部AI生成的漫剧作品",
  "tags": ["AI漫剧", "短视频", "国风"],
  "cover": "output/cover.jpg",
  "thumbnail": "output/thumbnail.jpg",
  "created_at": "2026-04-17T00:00:00.000Z",
  "duration": 90
}
```

### 标题截断规则

当标题长度超过平台限制时：

```
截断后的标题 = 原标题.substring(0, 最大长度 - 3) + "..."
```

例如：小红书标题限制 20 字符，原标题 25 字符 -> 截断为前 17 字符 + "..."

### 标签限制规则

```
平台标签 = 基础标签.slice(0, 平台标签最大数量)
```

### Hashtag 自动生成

每个平台自动追加平台专属标签：

| 平台 | 专属标签 |
|------|---------|
| 抖音 | #抖音热门 #DOU+小助手 |
| 快手 | #快手热门 #快手创作者 |
| 小红书 | #小红书 #笔记 |
| 视频号 | #视频号 #微信视频号 |

**生成规则**：

```
hashtag = [...用户标签, ...平台专属标签].map(tag => `#${tag}`).join(' ')
```

### 平台元数据输出

```json
{
  "douyin": {
    "title": "截断后的标题",
    "description": "截断后的描述",
    "tags": ["标签1", "标签2", ...],
    "cover": "output/cover.jpg",
    "hashtags": "#AI漫剧 #短视频 #国风 #抖音热门 #DOU+小助手"
  }
}
```

---

## 平台变体生成

### 时长检查

- 计算视频总时长（所有镜头时长之和）
- 与每个平台的最大时长比较
- 超过限制时标记为 `warning`

### 变体输出

```json
{
  "douyin": {
    "file_path": "output/douyin_version.mp4",
    "platform_name": "抖音",
    "settings": {
      "resolution": "1080x1920",
      "aspect_ratio": "9:16",
      "bitrate": "8Mbps",
      "frame_rate": 30,
      "file_format": "mp4"
    },
    "duration_check": "pass",
    "optimized": true
  }
}
```

---

## 导出清单（Export Manifest）

```json
{
  "version": "1.0.0",
  "exported_at": "2026-04-17T00:00:00.000Z",
  "master_video": "output/final_master.mp4",
  "platform_variants": { ... },
  "metadata": { ... },
  "quality_report": { ... },
  "export_log": [
    "Master video rendered: output/final_master.mp4",
    "Platform variants: douyin, kuaishou, xiaohongshu",
    "Quality score: 0.90"
  ]
}
```

---

## 输出格式

```json
{
  "phase": "export",
  "output": {
    "final_video": "output/final_master.mp4",
    "platform_variants": {
      "douyin": "output/douyin_version.mp4",
      "kuaishou": "output/kuaishou_version.mp4",
      "xiaohongshu": "output/xiaohongshu_version.mp4",
      "shipinhao": "output/shipinhao_version.mp4"
    },
    "platform_details": { ... },
    "quality_report": {
      "video_quality": "pass",
      "audio_quality": "pass",
      "compliance_check": "pass",
      "overall_score": 0.9,
      "checks": [ ... ],
      "issues": []
    },
    "metadata": {
      "title": "AI漫剧作品",
      "description": "这是一部AI生成的漫剧作品",
      "tags": ["AI漫剧", "短视频", "国风"],
      "cover": "output/cover.jpg",
      "thumbnail": "output/thumbnail.jpg",
      "created_at": "2026-04-17T00:00:00.000Z",
      "duration": 90
    },
    "platform_metadata": {
      "douyin": { ... },
      "kuaishou": { ... },
      "xiaohongshu": { ... },
      "shipinhao": { ... }
    },
    "export_manifest": "output/export_manifest.json"
  }
}
```

---

## 使用方法

### SOLO 执行步骤

1. 接收剪辑合成模块的成片和字幕文件
2. 接收目标平台列表（默认：抖音、快手、小红书、视频号）
3. 接收元数据（标题、描述、标签）
4. 为每个目标平台生成适配版本：
   - 检查时长是否超过平台限制
   - 应用平台的分辨率、码率、帧率设置
5. 执行质量校验
6. 生成平台专属元数据：
   - 标题截断
   - 描述截断
   - 标签数量限制
   - Hashtag 自动生成
7. 生成导出清单文件
8. 输出完整的导出报告

### 可用操作

| 操作 | 说明 |
|------|------|
| render | 执行完整的导出流程 |
| list_platforms | 列出所有支持的平台及详细配置 |
| preview_export | 预览指定平台的导出效果（元数据预览） |
| get_platform_requirements | 获取指定平台的技术要求 |

---

## 反模式

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 不检查时长限制直接导出 | 超过平台限制的视频无法上传 | 必须检查每个平台的最大时长 |
| 标题不截断 | 超长标题被平台截断，可能断在奇怪的位置 | 按平台限制截断，末尾加"..." |
| 标签数量不限制 | 超出平台限制的标签会被丢弃 | 按平台的 tags_max_count 截断 |
| 不生成 hashtag | 缺少平台专属标签影响曝光 | 自动追加平台专属标签 |
| 忽略质量校验 | 低质量视频影响用户体验 | 检查所有质量项目，确保合规 |
| 不生成导出清单 | 缺少发布记录，难以追溯 | 生成 export_manifest.json 记录完整导出信息 |

---

_Powered by Dola - 赛博合伙人出品 - v1.0.0_
