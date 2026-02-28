# TASK - llm-web-arena

## Step
4

## Phase
3 – Group Chat Round 1

## Goal
为 ChatGPT、Gemini、Grok 三个站点实现“群聊 Round 1”功能：每个 AI 独立回答相同话题并记录完整对话历史。所有代码必须在 uv 虚拟环境中运行。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（目录：`llm-web-arena`）

## Required Code Changes (in work repo)

1. **完善 Site Adapter**  
   - 确保 `adapters/chatgpt.py`、`adapters/gemini.py`、`adapters/grok.py` 均实现统一接口：  
     * `send(text: str)`: 将 prompt 输入到对应站点（包含随机延迟）。  
     * `read_latest_reply_text() -> str`: 返回最新一条完整回复文本。  
     * `wait_reply_done() -> str`: 调用 reply_done.py 中的判定逻辑，等待回复结束并返回完整文本。  
   - 如适用，先在各 adapter 中调用已有的 `resolver.find_first` 查找输入框、发送按钮、回复容器。

2. **实现 Group Chat Round 1 Orchestrator**  
   - 在 `orchestrator.py` 中新增 `GroupChatRound1.run(topic: str)`：  
     * 使用 `ThreadPoolExecutor` 并发或顺序（时间差 < 3 秒）向三家 AI 发送相同的 topic。  
     * 调用各 adapter 的 `wait_reply_done()` 获取完整回复；  
     * 将话题和回复以结构化形式写入 `conversation_history.json`（位于 `outputs/llm-web-arena`），格式示例：  
       ```json
       {
         "messages": [
           {"ai": "chatgpt", "role": "system", "timestamp": "...", "content": "…"},
           {"ai": "chatgpt", "role": "assistant", "timestamp": "...", "content": "…"},
           {"ai": "gemini", "role": "assistant", "timestamp": "...", "content": "…"},
           {"ai": "grok", "role": "assistant", "timestamp": "...", "content": "…"}
         ]
       }
       ```  
     * 结果保存在 `outputs/llm-web-arena/conversation_history.json`。

3. **新增 CLI 模式**  
   - 在 `cli.py` 中添加 `--mode groupchat_round1`，参数包括 `--topic`。  
   - 调用 `GroupChatRound1.run(topic)` 并完成上述输出。

4. **更新依赖与虚拟环境**  
   - 如有新增依赖（例如 `concurrent.futures` 已内置但其他包需安装），必须使用 `uv pip install` 安装，并更新 `requirements.txt` / `pyproject.toml`。  
   - 所有运行命令均使用 uv 环境，如 `uv run python3 -m web_llm_arena.cli ...`。

## Commands to Run

```bash
# 在私有仓库根目录，确保 uv 环境和依赖最新
uv venv           # 如未创建虚拟环境
uv pip install -r requirements.txt

# 运行 Round 1 群聊
uv run python3 -m web_llm_arena.cli \
  --mode groupchat_round1 \
  --topic "2026 年最佳编程语言是什么？给出理由和实施方案" \
  --config config.example.json
```
