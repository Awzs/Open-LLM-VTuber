<h1 align="center">培智学校“学伴”数字人部署</h1>

[![license](https://img.shields.io/github/license/Awzs/Open-LLM-VTuber)](https://github.com/Awzs/Open-LLM-VTuber/blob/main/LICENSE)
[项目主页](https://github.com/Awzs/Open-LLM-VTuber) | [问题反馈](https://github.com/Awzs/Open-LLM-VTuber/issues)
</h3>

本仓库基于 [t41372/Open-LLM-VTuber](https://github.com/t41372/Open-LLM-VTuber) 二次开发，感谢原项目的开源与持续维护。我们在其基础上面向培智学校场景增加“课堂记忆”“主动发言策略”“本地化部署模板”等能力，使数字人能够安全、可靠地服务校内教学。

> [常见问题文档](https://docs.qq.com/pdf/DTFZGQXdTUXhIYWRq)
>
> [用户调查问卷(英文)](https://forms.gle/w6Y6PiHTZr1nzbtWA)
>
> [用户调查问卷(中文)](https://wj.qq.com/s2/16150415/f50a/)



> :warning: 本项目仍处于早期阶段，目前正在**积极开发中**。

> :warning: 如果你想远程运行服务端并在其他设备上访问 (比如在电脑上运行服务端，在手机上访问)，由于前端的麦克风功能仅能在安全环境下使用（即 https 或 localhost），请参阅 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)，你需要配置反向代理和 https 才能在非本机 (non-localhost) 上正常访问。



# ⭐️ 项目简介


**Open-LLM-VTuber** 是我们为培智学校学生打造的**课堂“学伴”数字人**：它常驻教室前方的大屏或老师电脑的桌面右下角，随时准备与学生对话、观察课堂并输出恰当的 Live2D 表情与姿态。整套方案在本地即可运行，便于校内以离线方式部署。

系统保留了多模态感知、语音交互、Live2D 表情等核心能力，并针对课堂场景加入“课堂记忆”与“主动发言”控制开关：

- **开启对话**：AI 会实时听见、看见教室中的互动，给出语音或文字反馈，配合姿势与表情增强表达力。
- **启用主动发言**：数字人可主动向学生提问、点评课堂状态，辅助老师保持互动节奏。
- **仅收集课堂信息**：关闭语音播报后，系统只记录当堂关键信息和事件，支持课后复盘与教学评价。

如果你需要自定义角色与行为逻辑，可参考仓库中的 [Live2D 定制指南](https://github.com/Awzs/Open-LLM-VTuber/blob/main/doc/live2d-customize-guide.md) 以及配置模板，自行为学校和班级创建专属学伴。


## 🎓 项目背景与目标

- **服务对象**：培智学校学生、特教教师及教研团队。数字人常驻教室前方大屏/教师电脑桌面右下角，提供陪伴与教学反馈。
- **部署方式**：全部组件支持离线运行，方便在校内服务器或教学终端本地部署，避免课堂数据外泄。
- **新增能力**：
  - “课堂记忆”与“教学评价”数据链路（依托 `src/open_llm_vtuber/conversations/` 与 `chat_history/` 持久化）。
  - “主动发言/旁白”调度（结合 `agent/` 能力，根据课堂状态触发提问或总结）。
  - `conf.yaml`、`config_templates/` 中加入教室场景默认配置与角色模板，降低布署门槛。
- **延续 upstream**：继续沿用 Live2D 前端、ASR/TTS/LLM 插件化框架，并保持与原仓库兼容，便于同步改进。


## 👀 效果演示

| ![](assets/i1.jpg) | ![](assets/i2.jpg) |
|:---:|:---:|
| ![](assets/i3.jpg) | ![](assets/i4.jpg) |


## ✨ 功能和亮点

- 🖥️ **跨平台支持**：完美支持 macOS、Linux 和 Windows。我们支持英伟达和非英伟达 GPU，可以选择在 CPU 上运行或使用云 API 处理资源密集型任务。部分组件在 macOS 上支持 GPU 加速。

- 🔒 **支持离线模式**：使用本地模型完全离线运行 - 无需联网。你的对话只会待在你的设备上，确保隐私安全。

- 💻 **好看且功能强大的网页和桌面客户端**：提供网页版和桌面客户端两种使用方式，桌宠模式可让数字学伴常驻屏幕角落，方便师生随时交流。

- 🎯 **高级交互功能**：
  - 👁️ 视觉感知，支持摄像头、屏幕录制和截图，让数字学伴了解课堂状态
  - 🎤 语音打断，无需耳机（AI 不会听到自己的声音）
  - 🫱 触摸反馈，可通过白板触控或鼠标拖拽与学伴互动
  - 😊 Live2D 表情，设置情绪映射让后端控制模型表情
  - 🐱 桌宠模式支持透明背景全局置顶和鼠标穿透
  - 🗣️ AI 主动说话功能，可根据课堂状态自动提问或总结
  - 💭 AI 内心 OS，让老师可看到“想法/情绪”而不播报
  - 💾 聊天记录与课堂事件持久化，方便课后复盘
  - 🌍 TTS 翻译支持（例如，用中文聊天的同时，AI 使用日语声音）

- 🧠 **广泛的模型支持**：
  - 🤖 大语言模型 (LLM)：Ollama、OpenAI（以及任何与 OpenAI 兼容的 API）、Gemini、Claude、Mistral、DeepSeek、智谱、GGUF、LM Studio、vLLM 等
  - 🎙️ 语音识别 (ASR)：sherpa-onnx、FunASR、Faster-Whisper、Whisper.cpp、Whisper、Groq Whisper、Azure ASR等
  - 🔊 语音合成 (TTS)：sherpa-onnx、pyttsx3、MeloTTS、Coqui-TTS、GPTSoVITS、Bark、CosyVoice、Edge TTS、Fish Audio、Azure TTS等

- 🔧 **高度可定制**:
  - ⚙️ **简单的模块配置**：通过简单的配置文件修改，即可切换各种功能模块，无需深入代码
  - 🎨 **角色随心定制**：导入自定义 Live2D 模型与人设 Prompt，让数字学伴符合班级文化与校内礼仪
  - 🧩 **Agent 自由实现**：继承并实现 Agent 接口，接入 HumeAI EVI、Mem0 等方案实现课堂辅助
  - 🔌 **良好的可扩展性**：模块化设计让你能轻松添加自己的 LLM、ASR、TTS 等模块实现，随时扩展新特性


## 🧱 系统架构与关键模块

```
┌────────────────────────────────────────────────┐
│ FastAPI + WebSocket 服务 (src/open_llm_vtuber/server.py) │
├────────────────────────────────────────────────┤
│ ServiceContext (service_context.py)：统一管理 LLM/ASR/TTS  │
├───────────────┬──────────────────────────────┤
│ WebsocketHandler │ Agent/课堂策略 │
│ (websocket_handler.py) │ (agent/) │
├───────────────┴──────────────────────────────┤
│ 音频处理：ASR 模块 (asr/)、TTS 模块 (tts/)、VAD 模块 (vad/) │
├────────────────────────────────────────────────┤
│ 记忆与配置：conversations/、chat_history/、config_manager/       │
└────────────────────────────────────────────────┘
```

- **实时服务层**：`server.py` 通过 FastAPI + WebSocket 与前端通信，负责推送动作/表情、接收语音与课堂事件。`websocket_handler.py` 维护连接生命周期，保障课中断线可恢复。
- **ServiceContext**：集中注入 LLM、ASR、TTS、VAD、Agent 等实例，保证模块之间通过统一接口交互，方便替换底层实现。
- **课堂策略层**：`agent/` 目录实现多种策略，可按课堂状态决定是否主动发言、提问或播报。该层也可以接入 HumeAI、Mem0 等能力，扩展更多课堂反馈模式。
- **多模态处理**：`asr/`、`tts/`、`vad/` 模块封装语音识别、语音合成和语音活动检测，可按设备性能切换本地/云端模型。
- **记忆体系**：`conversations/` 与 `chat_history/` 存储课堂对话、情绪和事件，配合“课堂记忆”功能支撑课后评价。`config_manager/` 负责热加载配置，支持在课堂间快速调整角色或模型。
- **前端与资源**：`frontend/` 子模块提供 Web 客户端，`live2d-models/`、`avatars/`、`backgrounds/` 维护 Live2D 素材。根据学校视觉规范可替换成自有资源。


## 🚀 部署与定制

1. **环境准备**  
   - 建议使用 Python 3.10–3.12，并通过 `uv sync` 安装依赖；`ffmpeg`、`portaudio` 等系统依赖需提前装好。  
   - 若部署在教室电脑，请提前确认麦克风、摄像头、白板触控权限。
2. **配置角色与模块**  
   - 以 `conf.yaml` 为入口，按照 `config_templates/` 中的课堂示例修改 LLM、ASR、TTS、Agent、Live2D 模型等配置。  
   - `characters/`、`prompts/`、`doc/live2d-customize-guide.md` 提供角色设定、姿态映射与 Live2D 调优参考。
3. **运行与验证**  
   - 使用 `uv run run_server.py` 启动服务端，前端可通过浏览器或桌面端连接。  
   - 在上课前完成“开启对话/主动发言/仅记录”三种模式的切换测试，确认课堂记忆可正确写入 `chat_history/`。
4. **运维建议**  
   - 校园网络建议启用 HTTPS 反向代理，以便移动终端可安全接入。  
   - 若升级依赖或模板，运行 `uv run upgrade.py` 并同步 `conf.yaml`；课堂记录如涉及学生隐私，请定期脱敏或清理。


## 🤝 贡献与致谢

- 欢迎通过 [GitHub Issues](https://github.com/Awzs/Open-LLM-VTuber/issues) 反馈学校场景需求，也欢迎提交 PR 分享定制化配置、Agent 策略或部署脚本。  
- 致谢 [t41372/Open-LLM-VTuber](https://github.com/t41372/Open-LLM-VTuber) 及其社区贡献者，本项目延续其 MIT 许可证并在此基础上优化培智学校应用体验。

# 🎉🎉🎉 相关项目

[ylxmf2005/LLM-Live2D-Desktop-Assitant](https://github.com/ylxmf2005/LLM-Live2D-Desktop-Assitant)
- 你的 Live2D 桌面助手，由大语言模型 (LLM) 驱动！支持 Windows 和 macOS，它可以感知你的屏幕，检索剪贴板内容，并用独特的声音响应语音命令。具有语音唤醒、歌唱功能和完整的电脑控制，与你最喜欢的角色无缝交互。


## 📜 第三方许可证

### Live2D 样本模型声明

本项目包含由 Live2D Inc. 提供的 Live2D 样本模型。这些资产根据 Live2D 免费素材许可协议和 Live2D Cubism 样本数据使用条款单独授权，不受本项目 MIT 许可证的约束。

本内容使用由 Live2D Inc. 拥有并享有版权的样本数据。样本数据的使用符合 Live2D Inc. 制定的条款和条件。（参见 [Live2D 免费素材许可协议](https://www.live2d.jp/en/terms/live2d-free-material-license-agreement/) 和 [使用条款](https://www.live2d.com/eula/live2d-sample-model-terms_en.html)）。

注意：对于商业用途，特别是对于中型或大型企业，使用这些 Live2D 样本模型可能需要额外的许可要求。如果您计划将本项目用于商业目的，请确保您已获得 Live2D Inc. 的适当许可，或使用不包含这些模型的项目版本。


## Star 历史

[![Star History Chart](https://api.star-history.com/svg?repos=Awzs/Open-LLM-VTuber&type=Date)](https://star-history.com/#Awzs/Open-LLM-VTuber&Date)
---
