# Mini-Coding-Agent

本文件夹包含一个轻量级独立的编程智能体：

- 代码：`mini_coding_agent.py`
- CLI：`mini-coding-agent`

这是一个极简的本地智能体循环，具有以下特性：

- 工作区快照收集
- 稳定的提示词和轮次状态
- 结构化工具
- 危险工具的审批处理
- 对话记录和记忆持久化
- 有边界的委托

当前模型后端基于 Ollama。

<img src="https://sebastianraschka.com/images/github/mini-coding-agent/1.webp" width="500px">

<br>

**更多详细教程即将发布**

&nbsp;

## 环境要求

你需要：

- Python 3.10+
- 安装 Ollama
- 本地已拉取一个 Ollama 模型

可选：

- `uv` 用于环境管理和 `mini-coding-agent` CLI 入口

本项目除了标准库外没有其他 Python 运行时依赖，如果你不想使用 `uv`，可以直接用 `python mini_coding_agent.py` 运行。

&nbsp;

## 安装 Ollama

在你的机器上安装 Ollama，使 `ollama` 命令在你的 shell 中可用。

官方安装链接：[ollama.com/download](https://ollama.com/download)

然后验证：

```bash
ollama --help
```

启动服务：

```bash
ollama serve
```

在另一个终端中，拉取一个模型。例如：

```bash
ollama pull qwen3.5:4b
```

Qwen 3.5 模型库：

- [ollama.com/library/qwen3.5](https://ollama.com/library/qwen3.5)

本项目默认使用 `qwen3.5:4b`。如果你的内存充足，值得尝试更大的模型如 `qwen3.5:9b` 或其他更大的 Qwen 3.5 变体。智能体只是将提示词发送到 Ollama 的 `/api/generate` 端点。

&nbsp;

## 项目设置

克隆仓库或你的 fork 并进入目录：

```bash
git clone https://github.com/rasbt/mini-coding-agent.git
cd mini-coding-agent
```

如果你先 fork 了，请使用你的 fork URL：

```bash
git clone https://github.com/<your-github-user>/mini-coding-agent.git
cd mini-coding-agent
```

&nbsp;

## 基本用法

启动智能体：

```bash
cd mini-coding-agent
uv run mini-coding-agent
```

不使用 `uv` 时，直接运行脚本：

```bash
cd mini-coding-agent
python mini_coding_agent.py
```

默认配置：

- 模型：`qwen3.5:4b`
- 审批模式：`ask`

具体使用示例，请参阅 [EXAMPLE.md](EXAMPLE.md)。

&nbsp;

## 审批模式

危险工具（如 shell 命令和文件写入）需要审批。

- `--approval ask`
  执行危险操作前提示确认（默认，推荐）
- `--approval auto`
  自动允许危险操作（更方便但有风险）
- `--approval never`
  拒绝执行危险操作

示例：

```bash
uv run mini-coding-agent --approval auto
```

&nbsp;

## 恢复会话

智能体会将会话保存到目标工作区根目录的以下位置：

```text
.mini-coding-agent/sessions/
```

恢复最新会话：

```bash
uv run mini-coding-agent --resume latest
```

恢复指定会话：

```bash
uv run mini-coding-agent --resume 20260401-144025-2dd0aa
```

&nbsp;

## 交互命令

在 REPL 内，斜杠命令由智能体直接处理，而不是作为普通任务发送给模型。

- `/help`
  显示可用交互命令列表
- `/memory`
  打印提炼后的会话记忆，包括当前任务、跟踪的文件和笔记
- `/session`
  打印当前已保存会话 JSON 文件的路径
- `/reset`
  清除当前会话历史和提炼记忆，但保持在 REPL 中
- `/exit`
  退出交互会话
- `/quit`
  退出交互会话；`/exit` 的别名

&nbsp;

## 主要 CLI 参数

```bash
uv run mini-coding-agent --help
```

不使用 `uv`：

```bash
python mini_coding_agent.py --help
```

CLI 参数在智能体启动前传递。使用它们来选择工作区、模型连接、恢复行为、审批模式和生成限制。

重要参数：

- `--cwd`
  设置智能体应检查和修改的工作区目录；默认值：`.`
- `--model`
  选择 Ollama 模型名称，如 `qwen3.5:4b`；默认值：`qwen3.5:4b`
- `--host`
  指定智能体连接的 Ollama 服务器 URL（通常不需要）；默认值：`http://127.0.0.1:11434`
- `--ollama-timeout`
  控制客户端等待 Ollama 响应的时长（通常不需要）；默认值：`300` 秒
- `--resume`
  通过 id 恢复已保存的会话，或使用 `latest`；默认值：启动新会话
- `--approval`
  控制危险工具的处理方式：`ask`、`auto` 或 `never`；默认值：`ask`
- `--max-steps`
  限制单个用户请求允许的模型和工具轮次数量；默认值：`6`
- `--max-new-tokens`
  限制每步的模型输出长度；默认值：`512`
- `--temperature`
  控制采样随机性；默认值：`0.2`
- `--top-p`
  控制核采样的生成；默认值：`0.9`

&nbsp;

## 示例

请参阅 [EXAMPLE.md](EXAMPLE.md)

&nbsp;

## 注意事项

- 智能体期望模型输出 `<tool>...</tool>` 或 `<final>...</final>`。
- 不同的 Ollama 模型会以不同的可靠性遵循这些指令。
- 如果模型不能很好地遵循格式，请使用更强的指令遵循模型。
- 智能体有意设计得小而优化以提高可读性，而非健壮性。
