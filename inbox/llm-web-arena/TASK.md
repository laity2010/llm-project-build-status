# TASK - llm-web-arena

## Step
6

## Phase
5 – Group Chat Round 3 (投票与最终报告)

## Goal
在已完成 Round 2 评分的基础上，让 ChatGPT、Gemini 与 Grok 根据所有评分投票选出最佳方案并说明理由，生成总排名与最终赢家，并输出完整的报告。

## Work Repo (private/local)
`/Users/daisor/Documents/Github/Ds2026/llm-web-arena`

## Status Repo (public)
`llm-project-build-status`（目录：`llm-web-arena`）

## Required Code Changes

1. **完善 orchestrator：新增 Round 3**  
   - 在 `orchestrator.py` 中新增 `GroupChatRound3.run()`：  
     * 读取 `outputs/llm-web-arena/scores.json` 中的评分结果；  
     * 对每个 AI 构建 prompt，列出所有评分及简要汇总，要求其只输出 JSON 格式：  
       ```json
       {
         "vote": "chatgpt|gemini|grok",
         "reason": "投票理由，不超过 200 字"
       }
       ```  
     * 发送给各 AI 并调用 `wait_reply_done()` 获取回复，解析 JSON；  
     * 如果回复不是有效 JSON，重试一次附带更严谨的格式要求；仍失败则将其投票记为 `null` 并在报告中注明。

2. **汇总投票与结果**  
   - 统计每个 AI 投出的票数；  
   - 如出现多数票（≥2 票），该方案为最终赢家；如票数打平，则使用 Round 2 中的平均得分决定胜负；  
   - 生成最终排名与胜者说明并保存到 `outputs/llm-web-arena/report_final.md` 或在 `scores.json` 中附加相应字段。

3. **更新对话历史和报告**  
   - 将 Round 3 的投票对话追加到 `conversation_history.json`；  
   - 在 `report.py` 中生成最终 Markdown 报告，包含所有轮次对话摘要、评分表、投票表和最终排名。报告存放在 `outputs/llm-web-arena/debate_result.md`。

4. **新增 CLI 模式**  
   - 在 `cli.py` 中添加 `--mode groupchat_round3`，执行 Round 3 流程并生成最终报告。

5. **使用 uv 管理环境**  
   - 如有新增依赖，使用 `uv pip install` 安装；  
   - 所有运行命令都通过 `uv run` 执行。

## Commands to Run

```bash
# 确保 uv 环境和依赖
uv venv
uv pip install -r requirements.txt

# 执行 Round 3 投票和最终报告
uv run python3 -m web_llm_arena.cli \
  --mode groupchat_round3 \
  --config config.example.json
```
