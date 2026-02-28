# TASK - llm-web-arena

## Step
5

## Phase
4 – Group Chat Round 2 (评分互评)

## Goal
根据 Round 1 的回答，让每个 AI 对另外两个 AI 的回答进行打分（1–10分）并提供理由。必须输出严格的 JSON 格式，更新对话历史，并计算各 AI 的总分和排名。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（目录：`llm-web-arena`）

## Required Code Changes (in work repo)

1. **扩展 orchestrator**  
   - 新增 `GroupChatRound2.run()`（或等价方法），从 `outputs/llm-web-arena/conversation_history.json` 提取 Round 1 的回复。  
   - 生成 Round 2 的 prompt：向每个 AI 发送另外两名 AI 的回答，并要求只返回 JSON：  
     ```json
     {
       "scores": {"chatgpt": <1-10>, "gemini": <1-10>, "grok": <1-10>},
       "reason": "评分理由，不超过 200 字"
     }
     ```
     自评分为 0。  
   - 调用各 adapter 的 `send()`、`wait_reply_done()`，解析返回的 JSON；若解析失败，重试一次并附上更明确的指令；仍失败则记录该 AI 的评分为 `null`。

2. **成绩汇总**  
   - 保存每个 AI 的评分结果，计算总分和平均分。  
   - 生成排名：平均分高者优先；平分可留待下一步决议。  
   - 将汇总结果写入 `outputs/llm-web-arena/scores.json` 或在 `reports/latest_summary.json` 中添加 `scores` 字段。

3. **更新对话历史**  
   - 将 Round 2 的评分对话追加到 `outputs/llm-web-arena/conversation_history.json`，保留时间戳和角色信息。

4. **新增 CLI 模式**  
   - 在 `cli.py` 中加入 `--mode groupchat_round2`，执行上述逻辑。  
   - 运行时直接读取已生成的 `conversation_history.json`，无须新的 `--topic` 参数。

5. **确保使用 uv 环境**  
   - 若新增依赖，使用 `uv pip install` 安装，并更新 `requirements.txt`/`pyproject.toml`。  
   - 所有命令必须通过 `uv run` 执行。

## Commands to Run

```bash
# 确保 uv 环境存在并安装依赖
uv venv
uv pip install -r requirements.txt

# 执行 Round 2 评分
uv run python3 -m web_llm_arena.cli --mode groupchat_round2 --config config.example.json
```
