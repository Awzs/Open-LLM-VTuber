# Repository Guidelines

## Project Structure & Module Organization
- `src/open_llm_vtuber/`：核心 FastAPI/WebSocket 代码，包含 `server.py`、`service_context.py`、`websocket_handler.py` 及 `agent`、`asr`、`tts`、`vad`、`config_manager`、`conversations` 等模块。
- `config_templates/`、`characters/`、`prompts/`：默认配置、角色设定与系统提示词；修改配置后需同步模板并运行 `uv run upgrade.py`。
- `live2d-models/`、`avatars/`、`assets/`、`backgrounds/`：Live2D 模型与前端素材；遵循 `LICENSE-Live2D.md`。
- `web_tool/`、`frontend/`：Web Demo 与 Git 子模块前端；`scripts/run_bilibili_live.py` 提供直播脚本，`doc/` 指向离线文档。

## Build, Test, and Development Commands
- `uv sync`：安装/更新依赖（Python 3.10–3.12）。
- `uv run run_server.py`：加载 `conf.yaml` 启动服务器，供网页或桌面端连接。
- `uv run run_server.py --verbose`：输出更详细的 WebSocket、音频及引擎调度日志。
- `uv run upgrade.py`：当模板或依赖变化时执行，保证配置向后兼容。
- `ruff check .` 与 `ruff format .`：静态检查与格式修正。
- `pre-commit run --all-files`：本地执行全部钩子，与 CI 行为一致。

## Coding Style & Naming Conventions
- 统一使用 4 空格缩进、类型注解与中文注释；保持函数短小，嵌套不超过三层。
- 模块、函数与配置键使用 snake_case，类名用 PascalCase，常量全大写。
- 配置或提示词改动优先抽象为配置项，避免硬编码；跨模块通信请复用 `ServiceContext` 与工厂接口。

## Testing Guidelines
- 当前仓库无单元测试目录；提交前需至少运行 Ruff + pre-commit，并在 Web/桌面端完成核心功能回归（语音对话、Live2D 表情、配置切换等）。
- 新增测试文件请命名为 `tests/test_<feature>.py` 并使用 `pytest`，覆盖主要分支；必要时提供录屏或日志链接。

## Commit & Pull Request Guidelines
- 参考 `git log --oneline` 的前缀风格（如 `Fix xxx`、`Add yyy`、`Merge branch ...`），一条 commit 专注单一变更。
- PR 描述需包含：变更摘要、涉及模块、运行的命令/测试结果、相关 issue 或讨论链接，若触及配置或部署请附操作步骤及截图。
- 对公共 API、默认配置或文档的修改需同步更新 `CLAUDE.md`、`README` 或 docs 仓库，并提醒用户执行 `uv run upgrade.py`。

## Security & Configuration Tips
- 不要将私有 API Key、模型文件或商业 Live2D 资源加入仓库；敏感配置放入 `.env` 或本地 `conf.yaml`。
- WebSocket 代理部署请使用 HTTPS（参考 `README`）以启用浏览器麦克风；远端访问须结合反向代理和证书。
