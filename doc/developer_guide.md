# 开发者使用指南

> 本文档针对正在增量开发 Open-LLM-VTuber 的同学，总结了当前仓库的运行状态、主要模块、配置方法，以及“桌宠模式”和角色切换等常见需求的操作流程。

## 1. 架构及当前状态概览

- **后端核心（`src/open_llm_vtuber/`）**：`ServiceContext` 负责为每个 WebSocket 连接克隆一份完整的 LLM/ASR/TTS/VAD/Live2D 上下文；`websocket_handler.py` 处理消息路由、配置切换、群聊管理和聊天历史；`agent/`、`asr/`、`tts/`、`vad/`、`conversations/`、`config_manager/` 等目录则封装了各类可插拔模块。
- **前端（`frontend/`）**：由桌面客户端和浏览器共享的 React SPA，提供 Live Mode & Pet Mode、Live2D 交互、设置抽屉、聊天记录、群聊等 UI；编译产物在 `frontend/assets/` 下，默认入口为 `index.html`。
- **默认运行状态（`conf.yaml`）**：
  - LLM：`agent_config.agent_settings.basic_memory_agent.llm_provider = gemini_llm`，调用 Gemini 2.5 Flash。
  - ASR：`sherpa_onnx_asr`，载入 SenseVoice 多语模型。
  - TTS：使用 `edge_tts` 默认音色 `zh-CN-XiaoxiaoNeural`。
  - VAD：未显式开启，默认跟随 ASR 模型的检测能力。
  - 角色：`character_config.conf_name = 'mao_pro'`，Live2D 模型同名，persona 为“小学学伴乐乐”。
- **开发命令**：
  1. `uv sync` —— 拉取/更新 python 依赖。
  2. `uv run run_server.py --verbose` —— 启动服务端并输出调度日志。
  3. `uv run upgrade.py` —— 模版或依赖调整后运行，保持配置向后兼容。
  4. 可选静态检查：`ruff check .`、`ruff format .`、`pre-commit run --all-files`。

## 2. 配置体系速览（`conf.yaml`）

| 区块 | 作用 | 常用字段与说明 |
| --- | --- | --- |
| `system_config` | 服务器运行参数及模板目录 | `host/port` 服务监听；`config_alts_dir` 指向 `characters/`，用于扫描可切换的角色配置；`tool_prompts` 控制系统提示词追加的工具指令。 |
| `character_config` | 角色外观、人格与 Live2D 设置 | `conf_name/conf_uid` 是前端展示与持久化 Key；`live2d_model_name` 需对应 `model_dict.json` 中的条目；`persona_prompt` 定义提示词；`agent_config`, `asr_config`, `tts_config`, `vad_config` 在此区块内可覆盖主配置。 |
| `agent_config` | 会话代理与 LLM 池 | `conversation_agent_choice` 决定调用的代理（当前为 `basic_memory_agent`）；`agent_settings.<agent>.llm_provider` 指定使用哪一份 `llm_configs`；`llm_configs` 中可同时维护 OpenAI 兼容、Ollama、Gemini、DeepSeek 等多套凭据，切换角色时会被按需覆写。 |
| `asr_config` | 语音识别 | `asr_model` 选择后端 (`sherpa_onnx_asr`、`fun_asr`、`faster_whisper`…)，对应子字段设置模型路径、线程、温度等。 |
| `tts_config` | 文本转语音 | `tts_model` 指定后端（例如 `edge_tts`、`gpt_sovits_tts`、`cosyvoice_tts`…）；每个后端下的二级字段用于配置 API Key、音色、速率等。 |
| `tts_preprocessor_config` | TTS 前的文本清洗与翻译 | `remove_special_char` 等开关用于过滤括号/特殊符号；`translator_config` 支持 `deeplx` 或腾讯翻译，把聊天文本转换成目标语言再送入 TTS。 |
| `live_config` | 直播集成功能 | 目前仅对接 B 站监听脚本（`scripts/run_bilibili_live.py`），需要在此填入 `room_ids` 和 `SESSDATA`。 |

> ✅ **最佳实践**：在 `characters/*.yaml` 中只保留需要覆盖的字段，其他都会继承根目录 `conf.yaml` 的值。修改配置后可用 `uv run upgrade.py` 让模板与校验保持最新。

## 3. 运行模式：Live Mode vs. Pet Mode

- **Live Mode**：包含完整的侧边栏、输入区、摄像头/屏幕捕获等组件，适合浏览器或桌面客户端窗口化运行。
- **Pet Mode**：将 Live2D 模型置于透明背景，可拖拽/右键调出菜单，在桌面端配合窗口层级实现“桌宠”。
- **桌面客户端（Electron）增强能力**：
  - 网页端无法直接开启透明窗口或鼠标穿透，因此 Electron 仍会拦截 `setMode('pet')` 并转交 `window.api.setMode` 给主进程，触发原生窗口调整。
  - Electron 专属事件：`renderer-ready-for-mode-change`、`mode-change-rendered`（位于 `frontend/assets/main-*.js` 的 `ModeProvider` 中）。
- **浏览器环境**：新版 `ModeProvider` 允许直接切换到 Pet Mode，并弹出提示说明“无法透明置顶，仅提供简化布局”。Live2D 会按照窗口尺寸自动缩放，因此可通过浏览器的“浮动窗口”“画中画”等功能模拟桌宠。
- **操作路径**：登录前端 → 左上角模式下拉框（Live Mode/Pet Mode） → 选择想要的模式；若当前为浏览器且首次切换到 Pet Mode，会看到 `Pet mode (experimental)` 的信息提示。

## 4. 角色管理与切换

### 4.1 characters 目录

- 位置：`characters/`
- 说明文件：[`characters/README.md`](../characters/README.md) 详细介绍了最小角色文件和进阶角色文件的写法。
- 关键字段：
  - `conf_name` → 前端列表显示名称。
  - `conf_uid` → 历史记录关联的唯一 ID，**必须保持唯一**。
  - `live2d_model_name` → 匹配 `model_dict.json` 中的 `name`，才能加载 Live2D 模型。
  - `persona_prompt` → 角色性格定义。
  - 可选覆写：`tts_config`、`agent_config`、`asr_config`、`vad_config` 等。

### 4.2 UI 中切换角色

1. 启动服务端 `uv run run_server.py` 并打开前端（桌面客户端或浏览器 `http://localhost:12393`）。
2. 在聊天页面右上角点击设置齿轮，选择 **角色/预设** 下拉框，或者直接在左上角头像右侧的“角色列表”中选择。
3. 前端会发送 `switch-config` 消息（`frontend` 中 `useSwitchCharacter` 钩子 → WebSocket `type: "switch-config"`）。
4. `WebSocketHandler._handle_config_switch` 调用 `ServiceContext.handle_config_switch`：
   - 如果选择 `conf.yaml`，直接重载根配置。
   - 如果选择 `characters/*.yaml`，先读入角色文件，再使用 `deep_merge` 把差异覆盖到当前 `character_config`。
5. 成功后，服务端会通过 `set-model-and-conf` 推送新的 Live2D 模型信息与 `conf_uid`，前端界面同步刷新。

> ⚠️ **切换前**：如果当前正在说话，UI 会自动调用 `interrupt`、`stopMic` 并显示 “New Character Loading...”，避免音频/状态残留。

### 4.3 手动新增角色教程

1. 复制 `characters/zh_米粒.yaml` 或任意示例文件，改名为 `my_character.yaml`。
2. 填写最小配置：
   ```yaml
   character_config:
     conf_name: 'My Character'
     conf_uid: 'my_character_001'
     character_name: '星野'
     persona_prompt: |
       你是一个沉稳的 AI 向导，善于用类比解释技术问题。
   ```
3. 如果要自定义 Live2D：把模型放进 `live2d-models/`，在 `model_dict.json` 中注册，并在角色文件里设置 `live2d_model_name`。
4. 自定义语音：在角色文件的 `tts_config` 中覆写 `tts_model` 与具体音色参数。
5. 保存后回到前端设置面板 → 角色列表即可看到新条目。

## 5. 运行模式（桌宠）与 Live2D 调整技巧

- Pet Mode 下的 Live2D 会使用 `useLive2DResize` 将画布扩展到整个窗口或浏览器可视区域，`model_info.kScale` 控制初始缩放（默认 `0.5`）。
- 鼠标滚轮缩放：在 `model_info.scrollToResize = true` 时启用，默认为 `true`。
- 右键菜单：Electron 桌面版可呼出自定义菜单，浏览器中会屏蔽右键事件（由于缺少 `window.api.showContextMenu` 实现）。
- 如果需要拖动 Live2D 但保持对话输入：在设置里关闭“自动开启麦克风”即可避免误触发。

## 6. 调试与升级建议

1. **配置修改后校验**：运行 `uv run upgrade.py`，让 `validate_config` 使用最新 schema 重新检测 `conf.yaml` 与角色文件。
2. **查看可选角色**：`scan_config_alts_directory(config_alts_dir)` 会扫描 `characters` 并自动把 `conf.yaml` 插入列表，可以在日志中看到 `Found config files: [...]`，便于定位命名问题。
3. **背景图片**：通过 WebSocket `fetch-backgrounds` 调用 `scan_bg_directory()`，图片需存放于 `backgrounds/`，支持 jpg/png/gif。
4. **Live2D 调整**：若在某角色下需要不同的 `kScale` 或 `pointerInteractive`，可直接在该角色文件的 `live2d_model` 区块覆写。
5. **模式切换日志**：前端 `ModeProvider` 会在 Electron 场景下发送 `renderer-ready-for-mode-change` 与 `mode-change-rendered`，若桌宠窗口异常，可以在开发者工具 Console 中观察这些事件是否触发。

---

如需更深入的开发说明，可参考官方 [docs.llmvtuber.com](https://docs.llmvtuber.com/) 的 Development Guide，或直接查阅 `src/open_llm_vtuber/service_context.py`、`websocket_handler.py` 与 `frontend/assets/main-*.js` 中的实现细节。
