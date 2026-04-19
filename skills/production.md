# 素材生成模块

> 版本：1.0.0
>
> 智能风格匹配引擎，视觉/音频风格自动适配，一致性检查

---

## 触发词

`素材生成` `风格匹配` `视觉风格` `音频风格` `风格引擎` `BGM选择` `配乐匹配` `风格一致性`

---

## 功能说明

素材生成模块是 AI 漫剧创作链路的第三步。接收故事策划的输出，通过智能风格匹配引擎自动选择最合适的视觉和音频风格，并生成素材清单。

核心能力：
- **智能风格匹配** -- 根据题材自动匹配视觉风格和音频风格
- **用户覆盖** -- 支持用户手动指定风格覆盖自动匹配
- **一致性检查** -- 检查视觉风格、音频风格与题材的匹配度
- **素材清单生成** -- 为每个镜头生成视觉素材和音频素材的文件规划

---

## 视觉风格库

共 5 种视觉风格，详见 `config/styles.md`：

| 风格ID | 名称 | 适用题材 | 风格提示词 |
|--------|------|---------|-----------|
| style_3d_gufeng | 3D国风写实 | 玄幻、古风、仙侠、武侠 | 3D国风写实，高精度CG渲染，中国传统美学，精致细节 |
| style_anime | 二次元动漫 | 甜宠、校园、都市 | 日系动漫风格，高清二次元，明亮色彩，精致线条 |
| style_realistic | 写实风格 | 都市、悬疑、家庭伦理 | 超写实风格，真人质感，真实光影，电影级画质 |
| style_cartoon | 卡通风格 | 甜宠、家庭伦理、儿童 | 卡通Q版风格，简洁线条，明亮色彩，可爱造型 |
| style_xianxia | 仙侠水墨 | 仙侠、武侠、玄幻 | 中国水墨丹青风格，仙气缭绕，意境深远，古典美学 |

### 视觉风格详细参数

#### style_3d_gufeng（3D国风写实）
- **推荐模型**：wan2.1_i2v_pro
- **色彩**：传统中国色，朱红、靛蓝、明黄、墨黑
- **光线**：柔和自然光，古典光影效果

#### style_anime（二次元动漫）
- **推荐模型**：wan2.1_i2v_pro
- **色彩**：鲜艳色彩，高饱和度，柔和渐变
- **光线**：明亮均匀光，动漫风格光影

#### style_realistic（写实风格）
- **推荐模型**：wan2.1_i2v_pro
- **色彩**：自然真实色彩，电影色调
- **光线**：真实自然光，电影布光效果

#### style_cartoon（卡通风格）
- **推荐模型**：wan2.1_i2v_pro
- **色彩**：明亮活泼色彩，高饱和度
- **光线**：均匀明亮光，卡通风格光影

#### style_xianxia（仙侠水墨）
- **推荐模型**：wan2.1_i2v_pro
- **色彩**：水墨色调，留白美学，淡雅色彩
- **光线**：柔和朦胧光，水墨意境光影

---

## 音频风格库

共 4 种音频风格，详见 `config/styles.md`：

| 风格ID | 名称 | 适用题材 | 乐器 | 情绪 | 节奏 |
|--------|------|---------|------|------|------|
| audio_gufeng | 古风配乐 | 玄幻、古风、仙侠、武侠 | 古筝、二胡、琵琶、笛子、古琴 | 古典优雅，意境深远 | 中速偏慢 |
| audio_modern | 现代流行 | 都市、甜宠、校园 | 电子合成器、吉他、鼓、贝斯 | 时尚活力，轻松愉悦 | 中速偏快 |
| audio_suspense | 悬疑惊悚 | 悬疑 | 合成器音效、低频贝斯、打击乐 | 紧张悬疑，惊悚压抑 | 变化节奏 |
| audio_romantic | 浪漫抒情 | 甜宠、都市 | 钢琴、小提琴、大提琴 | 浪漫温馨，抒情动人 | 中速偏慢 |

---

## 风格匹配逻辑

### 自动匹配流程

1. 如果用户指定了视觉风格覆盖（`user_visual_override`），优先使用用户指定的风格
2. 否则，遍历视觉风格库，找到 `applicable_topics` 包含当前题材的风格
3. 取第一个匹配的风格作为默认选择
4. 音频风格匹配逻辑相同
5. 如果没有匹配的风格，使用风格库中的第一个

### 题材-风格映射

| 题材 | 默认视觉风格 | 默认音频风格 |
|------|------------|------------|
| 玄幻 | style_3d_gufeng | audio_gufeng |
| 都市 | style_realistic | audio_modern |
| 甜宠 | style_anime | audio_romantic |
| 悬疑 | style_realistic | audio_suspense |
| 武侠 | style_3d_gufeng | audio_gufeng |
| 古风 | style_3d_gufeng | audio_gufeng |
| 家庭伦理 | style_realistic | audio_modern |

---

## 一致性检查机制

### 检查项目

| 检查项 | 扣分规则 | 说明 |
|--------|---------|------|
| 未找到匹配的视觉风格 | -0.3 | 风格库中无适用题材 |
| 视觉风格与题材不完全匹配 | -0.1 | 用户覆盖的风格不包含当前题材 |
| 未找到匹配的音频风格 | -0.3 | 风格库中无适用题材 |
| 音频风格与题材不完全匹配 | -0.1 | 用户覆盖的风格不包含当前题材 |
| 视觉和音频风格适用题材不匹配 | -0.2 | 两种风格没有共同适用的题材 |

### 评分标准

- **总分 1.0**（满分）
- **>= 0.7**：通过（pass）
- **< 0.7**：警告（warning）

### 一致性报告格式

```json
{
  "visual_consistency": true,
  "audio_consistency": true,
  "cross_consistency": true,
  "overall_score": 1.0,
  "issues": []
}
```

---

## 素材清单生成

### 视觉素材

为分镜表中的每个镜头生成视觉素材规划：

```json
{
  "shot_id": 1,
  "asset_type": "image",
  "file_path": "assets/visual/shot_1.png",
  "style_applied": "3D国风写实",
  "consistency_check": "pass"
}
```

### 音频素材

#### 配音清单

为有台词的镜头生成配音规划：

```json
{
  "character": "主角",
  "file_path": "assets/audio/voiceover_1.mp3",
  "emotion": "neutral",
  "duration": 5
}
```

#### 背景音乐

```json
{
  "file_path": "assets/audio/bgm.mp3",
  "style_applied": "古风配乐"
}
```

#### 环境音效

```json
{
  "type": "环境音",
  "file_path": "assets/audio/ambient.mp3",
  "timestamp": 0
}
```

---

## 输出格式

```json
{
  "phase": "production",
  "output": {
    "matched_styles": {
      "topic": "玄幻",
      "visual": {
        "id": "style_3d_gufeng",
        "name": "3D国风写实",
        "description": "高精度CG渲染，中国风元素",
        "applicable_topics": ["玄幻", "古风", "仙侠", "武侠"],
        "config": { ... }
      },
      "audio": {
        "id": "audio_gufeng",
        "name": "古风配乐",
        "description": "古筝、二胡等民族乐器",
        "applicable_topics": ["玄幻", "古风", "仙侠", "武侠"],
        "config": { ... }
      }
    },
    "visual_assets": [
      { "shot_id": 1, "asset_type": "image", "file_path": "assets/visual/shot_1.png", "style_applied": "3D国风写实", "consistency_check": "pass" }
    ],
    "audio_assets": {
      "voiceovers": [
        { "character": "主角", "file_path": "assets/audio/voiceover_1.mp3", "emotion": "neutral", "duration": 5 }
      ],
      "bgm": {
        "file_path": "assets/audio/bgm.mp3",
        "style_applied": "古风配乐"
      },
      "sound_effects": [
        { "type": "环境音", "file_path": "assets/audio/ambient.mp3", "timestamp": 0 }
      ]
    },
    "style_config": {
      "visual_style": "style_3d_gufeng",
      "audio_style": "audio_gufeng",
      "visual": { ... },
      "audio": { ... }
    },
    "consistency_report": {
      "visual_consistency": true,
      "audio_consistency": true,
      "cross_consistency": true,
      "character_consistency": "pass",
      "style_consistency": "pass",
      "overall_score": 1.0
    }
  }
}
```

---

## 使用方法

### SOLO 执行步骤

1. 接收故事策划模块的输出（分镜表、角色圣经、场景库、风格配置）
2. 执行风格匹配：
   - 如果用户指定了风格覆盖，使用用户指定的
   - 否则根据题材自动匹配视觉和音频风格
3. 执行一致性检查，生成一致性报告
4. 为每个镜头生成视觉素材清单
5. 为有台词的镜头生成配音清单
6. 生成 BGM 和环境音效规划
7. 输出完整的素材生成报告

### 可用操作

| 操作 | 说明 |
|------|------|
| generate | 执行完整的素材生成流程 |
| match_styles | 仅执行风格匹配，返回匹配结果 |
| check_consistency | 检查指定风格的匹配一致性 |
| list_visual_styles | 列出所有可用的视觉风格 |
| list_audio_styles | 列出所有可用的音频风格 |

---

## 反模式

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 不做一致性检查直接使用风格 | 视觉和音频风格可能不搭 | 必须执行一致性检查，overall_score >= 0.7 才通过 |
| 忽略用户风格覆盖 | 用户明确想要的风格被忽略 | 用户指定的风格优先级最高 |
| 不检查风格与题材的匹配 | 古风配乐配都市题材会很违和 | 检查 applicable_topics 是否包含当前题材 |
| 素材清单缺少文件路径规划 | 后续剪辑时找不到文件 | 每个素材都要有明确的 file_path |
| 不生成配音清单 | 有台词的镜头缺少配音 | 遍历分镜表，为有 dialogue 的镜头生成配音 |

---

_Powered by Dola - 赛博合伙人出品 - v1.0.0_
