
![](./assets/banner.cn.jpg)

<h1 align="center">Open-LLM-VTuber</h1>
<h3 align="center">培智学校“学伴”数字人部署</h3>

[![license](https://img.shields.io/github/license/Awzs/Open-LLM-VTuber)](https://github.com/Awzs/Open-LLM-VTuber/blob/main/LICENSE)
[项目主页](https://github.com/Awzs/Open-LLM-VTuber) | [问题反馈](https://github.com/Awzs/Open-LLM-VTuber/issues)
</h3>

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


## 👀 效果演示

| ![](assets/i1.jpg) | ![](assets/i2.jpg) |
|:---:|:---:|
| ![](assets/i3.jpg) | ![](assets/i4.jpg) |


## ✨ 功能和亮点

- 🖥️ **跨平台支持**：完美支持 macOS、Linux 和 Windows。我们支持英伟达和非英伟达 GPU，可以选择在 CPU 上运行或使用云 API 处理资源密集型任务。部分组件在 macOS 上支持 GPU 加速。

- 🔒 **支持离线模式**：使用本地模型完全离线运行 - 无需联网。你的对话只会待在你的设备上，确保隐私安全。

- 💻 **好看且功能强大的网页和桌面客户端**：提供网页版和桌面客户端两种使用方式，支持丰富的交互功能和个性化设置，桌面客户端还可以在窗口模式和桌宠模式之间自由切换，让 AI 伴侣随时陪伴在身边

- 🎯 **高级交互功能**：
  - 👁️ 视觉感知，支持摄像头、屏幕录制和截图，让 AI 伙伴能看到你和你的屏幕
  - 🎤 语音打断，无需耳机（AI 不会听到自己的声音）
  - 🫱 触摸反馈，可以通过点击或拖拽与 AI 伙伴互动
  - 😊 Live2D 表情，设置情绪映射让后端控制模型表情
  - 🐱 宠物模式，支持透明背景全局置顶和鼠标穿透 - 可以将你的 AI 伙伴拖到屏幕上的任意位置
  - 🗣️ AI 主动说话功能
  - 💭 AI 内心 OS，AI 的表情、想法和动作可以被看到，但不会被读出来
  - 💾 聊天记录持久化，可以随时切换到以前的对话
  - 🌍 TTS 翻译支持（例如，用中文聊天的同时，AI 使用日语声音）

- 🧠 **广泛的模型支持**：
  - 🤖 大语言模型 (LLM)：Ollama、OpenAI（以及任何与 OpenAI 兼容的 API）、Gemini、Claude、Mistral、DeepSeek、智谱、GGUF、LM Studio、vLLM 等
  - 🎙️ 语音识别 (ASR)：sherpa-onnx、FunASR、Faster-Whisper、Whisper.cpp、Whisper、Groq Whisper、Azure ASR等
  - 🔊 语音合成 (TTS)：sherpa-onnx、pyttsx3、MeloTTS、Coqui-TTS、GPTSoVITS、Bark、CosyVoice、Edge TTS、Fish Audio、Azure TTS等

- 🔧 **高度可定制**:
  - ⚙️ **简单的模块配置**：通过简单的配置文件修改，即可切换各种功能模块，无需深入代码
  - 🎨 **角色随心定制**：导入自定义 Live2D 模型，让你的 AI 伴侣拥有独特外观。通过修改 Prompt，塑造你 AI 伴侣的人设。进行音色克隆，让你的 AI 伴侣有着你想要的声线
  - 🧩 **Agent自由实现**：继承并实现 Agent 接口，接入任何架构的 Agent，如 HumeAI EVI、OpenAI Her、Mem0 等
  - 🔌 **良好的可扩展性**：模块化设计让你能轻松添加自己的 LLM、ASR、TTS 等模块实现，随时扩展新特性


## 👥 用户评价
> 感谢开发者把女朋友开源分享出来让大家一起使用
> 
> 该女友使用次数已达 10w+

## 🚀 快速上手

请阅读 `doc/` 目录内的部署说明（例如 [live2d-customize-guide.md](https://github.com/Awzs/Open-LLM-VTuber/blob/main/doc/live2d-customize-guide.md)）以完成安装与本地化配置。



## ☝ 更新
> :warning: `v1.0.0` 版本有重大变更，需要重新部署。你*仍然可以*通过以下方法更新，但 `conf.yaml` 文件不兼容，并且大多数依赖项需要使用 `uv` 重新安装。如果你是准备从`v1.0.0`之前的版本升级到 `v1.0.0` 或之后的版本，建议按照 `doc/` 目录中的部署指南重新部署本项目。

如果你使用的是 `v1.0.0` 之后的版本，请运行更新脚本 `uv run upgrade.py` 进行更新。

## 😢 卸载
大多数文件，包括Python依赖和模型，都存储在项目文件夹中，所以删除项目文件夹就十分足够了。

然而，通过ModelScope或Hugging Face下载的模型可能也存在于`MODELSCOPE_CACHE`或`HF_HOME`中。虽然我们尽量将它们保存在项目的`models`目录中，但再次检查也无妨。

查看安装指南中，你所安装但可能不再需要的任何额外工具，例如`uv`、`ffmpeg`或`deeplx`。

## 🤗 想为项目做贡献?
请直接通过 [GitHub Issues](https://github.com/Awzs/Open-LLM-VTuber/issues) 讨论需求或提交 PR，我们会结合学校实际教学场景安排开发优先级。

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
