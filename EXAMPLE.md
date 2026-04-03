# 交互示例

这是一个在小型 Python 项目中使用 `mini-coding-agent` 配合 Ollama 的实战工作流示例。

流程如下：

1. 创建一个全新的仓库
2. 启动智能体
3. 实现 `binary_search.py`
4. 编辑实现
5. 添加 `pytest` 测试
6. 运行测试
7. 修复任何失败的测试

本示例假设：

- `ollama serve` 已在运行
- 你已经拉取了模型如 `qwen3.5:4b`（例如通过 `ollama pull qwen3.5:4b`）
- 你已经克隆或 fork 了 `rasbt/mini-coding-agent`
- 你已在本地 `mini-coding-agent` 文件夹中运行了 `uv sync`

如果你的内存充足，考虑使用更大的 Qwen 3.5 模型：

- [ollama.com/library/qwen3.5](https://ollama.com/library/qwen3.5)

&nbsp;

## 1. 创建一个全新的仓库

```bash
cd mini-coding-agent
mkdir -p ./tmp/binary-search-repo
cd ./tmp/binary-search-repo
git init
```

此时仓库基本上是空的：

```bash
ls -la
```

&nbsp;

## 2. 启动智能体

从你的 `mini-coding-agent` 克隆中启动智能体，但指向新仓库：

```bash
cd mini-coding-agent
uv run mini-coding-agent \
  --cwd ./tmp/binary-search-repo \
  --model "qwen3.5:4b"
```

<img src="https://sebastianraschka.com/images/github/mini-coding-agent/1.webp" width="500px">

&nbsp;

## 3. 让它实现二分搜索

在 `mini-coding-agent>` 提示符下，粘贴：

```text
检查这个仓库并创建一个 minimal 的 binary_search.py 文件。实现一个迭代的 binary_search(nums, target) 函数，用于在已排序的整数列表中查找。如果目标存在则返回其索引，不存在则返回 -1。保持代码非常简洁。
```

智能体完成后，在另一个终端或代码编辑器中检查结果。内容如下：

<img src="https://sebastianraschka.com/images/github/mini-coding-agent/2.webp" width="200px">

&nbsp;

## 4. 让它编辑实现

现在做一个小的后续更改。回到智能体 REPL，粘贴：

```text
更新 binary_search.py，使其在输入列表未按升序排序时抛出 ValueError。保持实现简单。
```

再次检查文件：

<img src="https://sebastianraschka.com/images/github/mini-coding-agent/3.webp" width="300px">

&nbsp;

## 5. 让它添加单元测试

回到 REPL，粘贴：

```text
创建 test_binary_search.py，包含 pytest 测试用例，覆盖：找到目标、未找到目标、空列表、第一个元素、最后一个元素，以及未排序输入抛出 ValueError。保持测试小而可读。
```

检查新的测试文件：

<img src="https://sebastianraschka.com/images/github/mini-coding-agent/4.webp" width="250px">

&nbsp;

## 6. 让它运行测试

回到 REPL，粘贴：

```text
为这个仓库运行 pytest。如果任何测试失败，修复代码或测试并重新运行，直到所有测试通过。
```

你也可以在另一个终端窗口中手动验证：

```
uv run pytest tmp/binary-search-repo
```

&nbsp;

## 7. 检查最终仓库状态

检查变更：

```bash
cd mini-coding-agent
cd ./tmp/binary-search-repo
git status --short
```

你现在应该有以下文件：

- `README.md`
- `binary_search.py`
- `test_binary_search.py`

&nbsp;

## 8. 有用的交互命令

智能体运行时，以下命令可用：

- `/help` 显示可用的斜杠命令及其功能说明。
- `/memory` 打印智能体当前会话的提炼工作记忆。
- `/session` 显示已保存会话 JSON 文件在磁盘上的路径。
- `/reset` 清除当前对话历史和工作记忆。
- `/exit` 离开交互式智能体。
