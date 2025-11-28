# CLAUDE.md

本文件面向 Claude Code（claude.ai/code），概述在本仓库协作时必须掌握的要点。

## 项目概览

Open-LLM-VTuber 是完全离线可运行的语音交互 AI 伴侣，包含实时语音、视觉感知和 Live2D 形象。后端由 FastAPI + WebSocket 构建，并通过模块化工厂模式管理 LLM、ASR、TTS、VAD 等引擎，核心代码位于 `src/open_llm_vtuber`。

## 必备命令

### 环境与运行
- `uv sync`：使用 uv 安装/同步 Python 依赖。
- `uv run run_server.py`：以默认配置启动服务端。
- `uv run run_server.py --verbose`：启动并输出详细日志，便于调试。
- `uv run upgrade.py`：运行升级脚本，同步配置或依赖改动。

### 质量保障
- `ruff check .`：执行 Ruff 静态检查。
- `ruff format .`：按项目风格格式化代码。
- `pre-commit run --all-files`：触发所有预提交钩子。

### 配置入口
- 用户配置：`conf.yaml`。
- 默认模版：`config_templates/conf.default.yaml`、`config_templates/conf.ZH.default.yaml`。
- 角色/人格：`characters/*.yaml`。

## 代码结构速览

- `run_server.py`：程序入口，解析 CLI、加载配置、启动 `WebSocketServer`。
- `src/open_llm_vtuber/server.py`：FastAPI + WebSocket 服务，托管静态资源与代理端点。
- `service_context.py`：依赖注入容器，每个连接独立实例管理引擎。
- `websocket_handler.py`：消息路由与连接管理，负责音频、事件与 Live2D 交互。
- `agent/`、`asr/`、`tts/`、`vad/`：各模块工厂与实现，遵循统一接口。
- `config_manager/`：类型安全配置加载与校验。
- `conversations/`：单聊、群聊与 TTS 管线。
- 资源目录：`live2d-models/`（模型）、`avatars/`、`assets/`、`backgrounds/`、`prompts/`、`characters/`。
- 前端：`frontend/`（Git 子模块，Web UI）与 `web_tool/`（简单录音 Demo）。
- 辅助：`scripts/run_bilibili_live.py`、`doc/`（指向在线文档）、`upgrade_codes/`（升级逻辑）。

## 架构要点

### WebSocket 服务
- `WebSocketServer` 负责挂载路由、静态文件与代理端点，并通过 `ServiceContext` 构造每个会话的依赖。
- `routes.py` 注册 FastAPI 接口，`websocket_handler.py` 以 `_handle_*` 约定管理消息类型。

### 模块化引擎
- Agent：`agent_factory.py` 根据配置实例化 basic_memory、Mem0、HumeAI EVI、Letta 等 Agent；`stateless_llm/` 内含 OpenAI、Claude、Ollama 等兼容实现。
- ASR：Sherpa-ONNX、FunASR、Faster-Whisper、Whisper.cpp 等通过工厂切换。
- TTS：Azure、Edge、MeloTTS、CosyVoice、GPT-SoVITS 等实现统一 `tts_manager` 管线，支持翻译与克隆。
- VAD：Silero VAD 负责讲话检测，确保打断体验。

### 会话与 Live2D
- `conversations/` 维护单人/群组对话状态、历史与流式 TTS。
- `live/`、`live2d_model.py` 等使后端能控制 Live2D 表情与动作。
- MCP 扩展：`mcpp/` 通过 Model Context Protocol 调用外部工具或服务器。

### 配置与升级
- 配置类集中于 `config_manager/`，由 `ruamel-yaml` 加载并校验。
- 变更需同步 `config_templates/` 与 `upgrade_codes/`，保持向后兼容。

## 开发准则

1. 新增引擎：定义接口 → 编写实现 → 注册至对应 `*_factory.py` → 扩展 `config_manager` → 更新默认模板。
2. 新增消息：扩展 `MessageType` → 实现 `_handle_xxx` → 在 `_init_message_handlers()` 注册 → 注意 `_cleanup_failed_connection` 以释放资源。
3. WebSocket/音频处理需保证实时性，避免阻塞协程；必要时使用后台任务或队列（如 `proxy_message_queue.py`）。
4. 涉及 Live2D/资产需确保遵循 `LICENSE-Live2D.md`，勿把商业模型直接加入仓库。

## 测试与质量

- 当前仓库暂无自动化测试目录，提交前至少运行 Ruff + pre-commit，并通过 Web/桌面客户端做人工回归。
- GitHub Actions（`CodeQL`、`Ruff`）会在 PR 中复核；保持 PR 小而专注，附变更说明与必要截图/日志。
- 依赖 uv，可在 CI / 本地使用 `uv pip compile` 生成 requirements（已有 `requirements*.txt`）。

## 常用目录索引

- `config_templates/`：默认配置，用作 diff 参考。
- `characters/`：自定义人格描述与语音设定。
- `prompts/`：系统提示词模版。
- `avatars/`、`backgrounds/`：前端素材。
- `model_dict.json`：已支持的模型/引擎映射。
- `upgrade_codes/`：分版本升级脚本与提示常量。

## 贡献提示

- 遵循 `git log --oneline` 所示的简单前缀（如“Fix ...”、“Add ...”、“Merge ...”），保持语义化。
- PR 需在描述中列出：变更摘要、测试方式、依赖更新、相关 issue/讨论链接。
- 修改配置或接口时，请同步更新文档（`README`、`docs/` 仓库）并告知需要运行 `uv run upgrade.py` 的场景。

保持中文注释、简洁函数、少层级缩进，优先通过配置扩展而非硬编码逻辑。
