# TASK - llm-web-arena

## Step
5

## Phase
4 – Group Chat Round 2 (评分互评)

## Goal
根据 Round 1 的回答，让每个 AI 对另外两个 AI 的回答进行打分（1–10 分）并解释理由。必须输出严格的 JSON 格式，更新对话历史，并计算各 AI 的总分和排名。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（目录：`llm-web-arena`）

## Required Code Changes (in work repo)

1. **扩展 orchestrator**  
   - 在 `orchestrator.py` 中新增 `GroupChatRound2.run()`（或等价方法）。  
   - 从 `outputs/llm-web-arena/conversation_history.json` 中读取 Round 1 的回复（每个 AI 的回答）。  
   - 构建 Round 2 的 prompt：向每个 AI 发送另外两名 AI 的回答，并要求只输出 JSON，例如：
     ```json
     {
       "scores": {"chatgpt": <1-10>, "gemini": <1-10>, "grok": <1-10>},
       "reason": "评分理由，不超过 200 字"
     }
     ```
     如果某 AI 不能自评，请对自己给 0 分。  
   - 调用各自的 adapter 的 `send()`、`wait_reply_done()` 获取回复，解析 JSON。  
   - 若返回的内容不是有效 JSON，则重试一次，附上更明确的指令；若仍失败，则记录该 AI 的评分为 `null` 并在报告中标记错误。

2. **成绩汇总**  
   - 将每个 AI 的评分结果保存到内存中，并计算：
     - 各 AI 获得的总分（对方给出的分数之和）。
     - 平均分。
   - 生成排名：平均分高者为先；若平局，可按字母顺序排列或留待下一阶段处理。

3. **更新对话历史与输出**  
   - 将 Round 2 的评分对话追加到 `outputs/llm-web-arena/conversation_history.json`（保留时间戳和角色信息）。  
   - 将汇总结果写入新的 `outputs/llm-web-arena/scores.json` 或在 `latest_summary.json` 中添加 `scores` 字段。

4. **新增 CLI 模式**  
   - 在 `cli.py` 中添加 `--mode groupchat_round2`，读入配置并执行 `GroupChatRound2.run()`。  
   - 运行时无需再传入 `--topic`，直接读取上一轮输出的 `conversation_history.json`。

5. **确保使用 uv 环境**  
   - 如有新增依赖（例如用于解析 JSON 的库），使用 `uv pip install` 安装，并更新 `requirements.txt` / `pyproject.toml`。  
   - 所有命令都应通过 `uv run` 运行。

## Commands to Run

```bash
# 确保 uv 环境已创建并安装依赖
uv venv
uv pip install -r requirements.txt

# 执行 Round 2 评分
uv run python3 -m web_llm_arena.cli \
  --mode groupchat_round2 \
  --config config.example.json
```
