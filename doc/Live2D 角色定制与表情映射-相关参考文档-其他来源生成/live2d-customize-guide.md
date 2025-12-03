# 🎨 Open-LLM-VTuber Live2D 角色定制完整指南

## 📚 目录
1. [快速开始](#快速开始)
2. [获取 Live2D 模型](#获取-live2d-模型)
3. [配置模型文件结构](#配置模型文件结构)
4. [emotion.yaml 表情映射详解](#emotiony-表情映射详解)
5. [实际案例配置](#实际案例配置)
6. [常见问题排查](#常见问题排查)

---

## 快速开始

### 文件夹结构
```
Open-LLM-VTuber/
├── models/
│   └── live2d_models/
│       ├── model_dict.json          ← 模型注册表
│       ├── your_character_v1/       ← 新角色文件夹
│       │   ├── model.model3.json    ← 模型配置
│       │   ├── model.moc3           ← 模型数据
│       │   ├── textures/            ← 纹理文件
│       │   ├── motions/             ← 动作文件
│       │   ├── expressions/         ← 表情文件
│       │   └── physics/             ← 物理配置
│       └── other_character_v2/
├── characters/                       ← 角色设置目录
│   ├── conf.yaml                    ← 默认配置
│   └── your_character.yaml          ← 新增角色配置
└── ...
```

### 三步快速添加新角色
```
第一步：准备 Live2D 模型文件
第二步：注册到 model_dict.json
第三步：配置 emotion.yaml 和角色 .yaml 文件
第四步：启动应用并选择新角色
```

---

## 获取 Live2D 模型

### 方式 1：使用 Open-LLM-VTuber 自带模型 ✅（推荐新手）
项目已包含免费可用的 Live2D 样本模型（由 Live2D Inc. 官方提供）

```bash
# 模型位置
models/live2d_models/Kohaku/
models/live2d_models/Shizuku/
models/live2d_models/Wanko/
```

**优势**：
- ✅ 已经过验证，配置齐全
- ✅ 无需额外下载
- ✅ 快速修改即可定制

### 方式 2：下载免费开源模型
推荐几个小学友好的卡通模型来源：

#### 🐱 ShiraLive2D（高质量免费模型）
- **下载链接**: https://shiralive2d.com/live2d-sample-models/
- **推荐模型**:
  - Lisette（女孩角色，28K+ 下载）
  - Komi（可爱角色）
- **优势**: 适合教育场景，卡通风格
- **文件包含**: .moc3, 纹理, 动作, 表情, 物理配置

#### 🎮 VTube Studio Steam Workshop
- **下载链接**: https://steamcommunity.com/app/1325860/workshop/
- **模型数量**: 数千个免费社区模型
- **优势**: 质量参差不齐但选择最多
- **适用**: 教育友好的模型标签筛选

#### 🌍 Live2D 官方样本
- **下载链接**: https://www.live2d.com/en/download/sample-data/
- **特点**: 官方维护，文档齐全
- **优势**: 可学习官方最佳实践

### 方式 3：使用现有 PSD 制作 🎨
如果你已有卡通角色（PPT、Procreate、Photoshop 等）：

```
购买 Live2D Cubism 软件（有免费版）
  ↓
导入你的角色 PSD/图片
  ↓
使用骨骼绑定（rigging）系统添加动作
  ↓
导出为 .moc3 格式
  ↓
导入 Open-LLM-VTuber
```

**推荐教程**: YouTube "Live2D rigging tutorial for beginners"

---

## 配置模型文件结构

### Step 1：准备模型文件夹

下载模型后，确保包含以下文件：

```
your_character_v1/
├── model.model3.json          [必需] 模型元数据
├── model.moc3                 [必需] 编译后的模型
├── textures/
│   ├── texture_00.png
│   ├── texture_01.png
│   └── ...
├── motions/                   [可选] 动作文件
│   ├── idle/
│   │   ├── idle_01.motion3.json
│   │   ├── idle_02.motion3.json
│   │   └── idle_03.motion3.json
│   ├── talk/
│   │   ├── talk_01.motion3.json
│   │   └── ...
│   └── ...
├── expressions/               [必需] 表情文件
│   ├── f01.exp3.json          (中立表情)
│   ├── f02.exp3.json          (伤心/害怕)
│   ├── f03.exp3.json          (愤怒)
│   ├── f04.exp3.json          (开心/惊喜)
│   └── ...
└── physics/                   [可选] 物理仿真
    └── physics.physics3.json
```

### Step 2：注册到 model_dict.json

打开 `models/live2d_models/model_dict.json` 文件：

```json
[
  {
    "name": "Kohaku",
    "path": "models/live2d_models/Kohaku/Kohaku.model3.json",
    "url": "models/live2d_models/Kohaku/Kohaku.model3.json",
    "kScale": 0.4,
    "initialXshift": 0,
    "initialYshift": 0.05,
    "idleMotionGroupName": "idle",
    "defaultEmotion": 0,
    "emotionMap": {
      "neutral": 0,
      "anger": 1,
      "disgust": 1,
      "fear": 2,
      "joy": 3,
      "sadness": 2,
      "surprise": 3,
      "smirk": 3
    }
  },
  {
    "name": "your_character_v1",           ← 新增模型
    "path": "models/live2d_models/your_character_v1/model.model3.json",
    "url": "models/live2d_models/your_character_v1/model.model3.json",
    "kScale": 0.5,                         ← 缩放比例（调整大小）
    "initialXshift": 0,                    ← 初始 X 位置偏移
    "initialYshift": 0.1,                  ← 初始 Y 位置偏移
    "idleMotionGroupName": "idle",         ← 空闲动作分组
    "defaultEmotion": 0,                   ← 默认表情索引
    "emotionMap": {                        ← 表情映射（见下节详解）
      "neutral": 0,
      "anger": 2,
      "disgust": 2,
      "fear": 1,
      "joy": 3,
      "sadness": 1,
      "surprise": 3,
      "smirk": 3
    }
  }
]
```

**参数解释**:

| 参数 | 说明 | 示例值 |
|------|------|--------|
| `name` | 模型唯一标识符（显示在前端选择菜单中） | `"classroom_buddy_v1"` |
| `path` | 本地模型路径 | `"models/live2d_models/..."` |
| `url` | 网页访问 URL（通常同 path） | 同上 |
| `kScale` | 缩放比例（0.3-1.0）| `0.5` |
| `initialXshift` | X 轴偏移（-1.0 到 1.0） | `-0.2` |
| `initialYshift` | Y 轴偏移（-1.0 到 1.0） | `0.15` |
| `idleMotionGroupName` | 空闲动作文件夹名称 | `"idle"` |
| `defaultEmotion` | 默认表情（用索引或名称） | `0` 或 `"neutral"` |
| `emotionMap` | 表情到索引/名称的映射 | 见下节 |
| `tapMotions` | 点击响应动作（可选） | `{"head": {"motion": ["talk"]}}` |

---

## emotion.yaml 表情映射详解

### 什么是 emotionMap？

`emotionMap` 将 AI 识别的 **情绪关键字** 映射到 Live2D 模型的 **具体表情**。

当 LLM 输出包含 `[emotion_keyword]` 时，系统会自动触发对应的表情动画。

### 表情映射的两种方式

#### 方式 A：使用表情索引（推荐）✅

```json
"emotionMap": {
  "neutral": 0,      // f01.exp3.json 的索引
  "anger": 2,        // f03.exp3.json 的索引
  "disgust": 2,      // 复用 f03 表情
  "fear": 1,         // f02.exp3.json 的索引
  "joy": 3,          // f04.exp3.json 的索引
  "sadness": 1,      // 复用 f02 表情
  "surprise": 3,     // 复用 f04 表情
  "smirk": 3         // 复用 f04 表情
}
```

**工作原理**:
```
Expressions 数组（model.model3.json 中）
[
  {Name: "f01", File: "f01.exp3.json"},  ← 索引 0
  {Name: "f02", File: "f02.exp3.json"},  ← 索引 1
  {Name: "f03", File: "f03.exp3.json"},  ← 索引 2
  {Name: "f04", File: "f04.exp3.json"}   ← 索引 3
]

emotionMap 配置
"joy": 3  →  表示 joy 对应上面的索引 3  →  即 f04.exp3.json
```

#### 方式 B：使用表情名称

```json
"emotionMap": {
  "neutral": "f01",      // 直接用表情文件名
  "anger": "f03",
  "disgust": "f03",
  "fear": "f02",
  "joy": "f04",
  "sadness": "f02",
  "surprise": "f04",
  "smirk": "f04"
}
```

**两种方式的区别**:

| 方式 | 优点 | 缺点 |
|------|------|------|
| 索引（数字） | 更灵活，可快速改映射 | 需要手动查看 Expressions 数组顺序 |
| 名称（字符串） | 直观易读，不易出错 | 修改时需改多个地方 |

### 实际配置示例：小学教室角色

#### 示例 1：简单卡通女孩
```json
{
  "name": "ClassroomBuddy",
  "path": "models/live2d_models/lisette/model.model3.json",
  "kScale": 0.6,
  "initialXshift": 0,
  "initialYshift": 0.1,
  "idleMotionGroupName": "idle",
  "defaultEmotion": 0,
  "emotionMap": {
    "neutral": 0,       // 微笑
    "anger": 2,         // 皱眉
    "disgust": 2,       // 同上，女孩不会很生气
    "fear": 1,          // 惊讶
    "joy": 0,           // 大笑（复用微笑）
    "sadness": 3,       // 哭泣
    "surprise": 1,      // 惊讶
    "smirk": 0          // 调皮微笑
  }
}
```

#### 示例 2：可爱宠物角色
```json
{
  "name": "PetBuddy",
  "path": "models/live2d_models/wanko/model.model3.json",
  "kScale": 0.45,
  "initialXshift": 0.1,
  "initialYshift": 0.2,
  "idleMotionGroupName": "idle",
  "defaultEmotion": 0,
  "emotionMap": {
    "neutral": 0,       // 开心
    "anger": 1,         // 生气摇尾巴
    "disgust": 1,       // 不开心
    "fear": 2,          // 害怕缩小
    "joy": 0,           // 特别开心
    "sadness": 2,       // 难过
    "surprise": 3,      // 惊讶
    "smirk": 0          // 调皮
  }
}
```

---

## 实际案例配置

### 完整示例：从下载到运行

#### Step 1：下载模型
```bash
# 从 ShiraLive2D 下载 Lisette 模型
# 解压到：models/live2d_models/lisette/
```

#### Step 2：注册到 model_dict.json
```bash
cd models/live2d_models/
# 编辑 model_dict.json，添加以下内容：
```

```json
{
  "name": "Lisette_Classroom",
  "path": "models/live2d_models/lisette/lisette.model3.json",
  "url": "models/live2d_models/lisette/lisette.model3.json",
  "kScale": 0.55,
  "initialXshift": 0,
  "initialYshift": 0.05,
  "idleMotionGroupName": "idle",
  "defaultEmotion": 0,
  "emotionMap": {
    "neutral": 0,
    "anger": 2,
    "disgust": 2,
    "fear": 1,
    "joy": 3,
    "sadness": 1,
    "surprise": 3,
    "smirk": 3
  },
  "tapMotions": {
    "head": {
      "motion": ["talk"]
    },
    "body": {
      "motion": ["idle"]
    }
  }
}
```

#### Step 3：创建角色配置文件
创建 `characters/lisette_classroom.yaml`：

```yaml
# Lisette 教室助手角色配置

# 角色名称
name: "丽塞特（教室助手）"

# 使用的 Live2D 模型
live2d_model_name: "Lisette_Classroom"

# LLM 系统提示词（定义角色性格）
prompt: |
  你是一个活泼、友善的教室助手。你的名字是丽塞特。
  
  你的特点：
  - 善于与小学生互动
  - 语言简单易懂
  - 鼓励和表扬学生
  - 能够讲笑话和有趣的故事
  - 会主动帮助学生学习
  
  当回答问题时：
  1. 用简单的中文解释
  2. 使用适当的 [emotion] 标签控制表情
  3. 要友好、耐心
  4. 避免复杂的专业术语
  
  表情标签说明（在你的回答中使用）：
  - [neutral] - 普通聊天时的微笑
  - [joy] - 非常开心或庆祝时
  - [surprise] - 惊讶或兴奋时
  - [sadness] - 同情或安慰时
  - [anger] - 不赞成某个行为时（很少用）

# 语音配置
tts:
  engine: "edge_tts"
  voice: "zh-CN-XiaoxiaoNeural"  # 年轻女性，适合教育
  speed: 1.0
  
# 语音识别配置
asr:
  engine: "faster_whisper"
  language: "zh"

# LLM 配置
llm:
  provider: "ollama"
  model: "qwen2:latest"  # 或其他本地模型
```

#### Step 4：启动应用
```bash
# 确保你在 Open-LLM-VTuber 根目录
uv run python server.py

# 打开浏览器
# http://localhost:5000

# 在前端界面左上角的角色选择中选择 "Lisette_Classroom"
```

---

## emotion.yaml 深入配置

### 高级配置选项

```json
{
  "name": "AdvancedCharacter",
  "path": "models/live2d_models/advanced/model.model3.json",
  "url": "models/live2d_models/advanced/model.model3.json",
  
  // 基础参数
  "kScale": 0.5,
  "initialXshift": 0,
  "initialYshift": 0.1,
  
  // 动作配置
  "idleMotionGroupName": "idle",
  "defaultEmotion": 0,
  
  // 核心：表情映射
  "emotionMap": {
    "neutral": 0,
    "anger": 2,
    "disgust": 2,
    "fear": 1,
    "joy": 3,
    "sadness": 1,
    "surprise": 3,
    "smirk": 3
  },
  
  // 点击交互动作
  "tapMotions": {
    "head": {
      "motion": ["talk_01", "talk_02"],  // 随机选择
      "weight": 1
    },
    "body": {
      "motion": ["idle_01"],
      "weight": 1
    },
    "other": {
      "motion": ["special_animation"],
      "weight": 0.5  // 50% 概率触发
    }
  }
}
```

### 常用情绪和表情对应

| 情绪 | 表现 | 教室场景例子 |
|------|------|--------------|
| `neutral` | 中立/微笑 | 一般聊天、讲课 |
| `joy` | 开心/大笑 | 赞美学生、庆祝成功 |
| `surprise` | 惊讶/兴奋 | "哇！你答对了！" |
| `sadness` | 难过/同情 | 安慰学生、讲悲伤故事 |
| `anger` | 不满/生气 | 很少使用，避免吓到孩子 |
| `fear` | 害怕/紧张 | 讲鬼故事 |
| `disgust` | 厌恶/皱眉 | 不建议在教室场景用 |
| `smirk` | 调皮/微笑 | 开玩笑、卖萌 |

### 自定义情绪关键字

如果你想添加自己的情绪关键字，修改 `emotionMap`：

```json
"emotionMap": {
  // 标准情绪（必需保留）
  "neutral": 0,
  "anger": 2,
  
  // 自定义情绪（可选）
  "excited": 3,        // 非常兴奋
  "confused": 1,       // 困惑
  "thinking": 0,       // 思考（使用中立表情）
  "proud": 3,          // 自豪
  "sleepy": 1          // 困倦
}
```

然后在系统提示词中告诉 LLM 可以使用这些标签：

```yaml
prompt: |
  你可以使用以下表情标签来控制你的表情：
  [neutral] [joy] [surprise] [sadness] [excited] [confused] [thinking] [proud] [sleepy]
  
  例如：
  "我非常 [excited] 能帮助你学习！"
  "让我 [thinking] 一下这个问题..."
```

---

## 常见问题排查

### ❌ 问题 1：模型加载失败 "Model not found"

**原因**：
- path 路径不正确
- 文件夹名称大小写不匹配
- model.model3.json 文件缺失

**解决方案**：
```bash
# 1. 检查文件是否存在
ls -la models/live2d_models/your_model/

# 2. 确认 model_dict.json 中的路径准确
# 3. 检查是否有拼写错误（Linux/Mac 对大小写敏感）

# 4. 验证 model.model3.json 文件存在
file models/live2d_models/your_model/model.model3.json
```

### ❌ 问题 2：表情不切换 "Emotions not changing"

**原因**：
- emotionMap 配置错误
- 表情索引超出范围
- LLM 没有输出 [emotion] 标签

**解决方案**：
```json
// ❌ 错误
"emotionMap": {
  "joy": 10  // 模型只有 4 个表情（0-3），索引 10 不存在
}

// ✅ 正确
"emotionMap": {
  "joy": 3   // 使用有效索引
}
```

**检查 LLM 输出**：
- 打开浏览器控制台（F12 → Console）
- 查看是否有 `[emotion]` 标签被识别
- 修改系统提示词，明确告诉 LLM 如何使用表情标签

### ❌ 问题 3：模型太大或太小

**原因**：
- kScale 参数设置不当

**解决方案**：
```json
// 调整 kScale 参数范围：0.2 - 1.0
"kScale": 0.3,    // 较小
"kScale": 0.5,    // 中等（推荐）
"kScale": 0.8,    // 较大
```

### ❌ 问题 4：模型位置偏移

**原因**：
- initialXshift 或 initialYshift 不合适

**解决方案**：
```json
// X 轴：-1.0（左边）到 1.0（右边）
// Y 轴：-1.0（下方）到 1.0（上方）

"initialXshift": -0.2,   // 向左偏移 20%
"initialYshift": 0.15,   // 向上偏移 15%
```

### ❌ 问题 5：网页版悬浮模式不工作

**原因**：
- 浏览器不支持或权限不足
- CSS 配置问题

**解决方案**：
```javascript
// 在前端配置中启用悬浮模式
// 可能需要在 conf.yaml 中添加：

web_config:
  floating_mode: true
  always_on_top: true  # 如果浏览器支持
  transparent_background: true
```

### ✅ 调试技巧

```bash
# 1. 查看浏览器控制台
# F12 → Console → 查找错误信息

# 2. 检查后端日志
# 运行 server.py 的终端会打印调试信息

# 3. 验证 JSON 格式
# 使用 https://jsonlint.com 验证 model_dict.json

# 4. 测试 LLM 输出
# 在聊天时，观察 LLM 是否输出 [emotion] 标签
# 检查系统提示词是否正确
```

---

## 快速参考卡片

### 最小化配置（快速开始）

```json
{
  "name": "MyCharacter",
  "path": "models/live2d_models/mychar/model.model3.json",
  "url": "models/live2d_models/mychar/model.model3.json",
  "kScale": 0.5,
  "defaultEmotion": 0,
  "emotionMap": {
    "neutral": 0,
    "anger": 1,
    "joy": 2,
    "sadness": 1,
    "surprise": 2
  }
}
```

### 教室优化配置（推荐）

```yaml
# characters/classroom_buddy.yaml

name: "教室伙伴"
live2d_model_name: "Lisette_Classroom"

prompt: |
  你是一个友善、耐心的教室助手...
  使用表情标签：[neutral] [joy] [surprise] [sadness]

tts:
  engine: "edge_tts"
  voice: "zh-CN-XiaoxiaoNeural"

llm:
  provider: "ollama"
  model: "qwen2:latest"
```

---

## 下一步

1. ✅ 选择一个 Live2D 模型
2. ✅ 注册到 model_dict.json
3. ✅ 配置表情映射（emotionMap）
4. ✅ 创建角色 .yaml 文件
5. ✅ 启动 server.py 并测试
6. ✅ 根据反馈微调参数
7. ✅ 部署到教室电脑

**问题或建议？**
- GitHub Issues: https://github.com/Open-LLM-VTuber/Open-LLM-VTuber/issues
- 中文常见问题文档: https://docs.qq.com/pdf/DTFZGQXdTUXhIYWRq
