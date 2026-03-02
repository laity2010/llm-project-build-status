# STATUS - llm-web-arena

## State
- IN_PROGRESS
- Current step: Step 5 / Phase 4 (`groupchat_round2`, blocked and redesign in progress)

## Latest Progress (2026-03-02)
- ChatGPT single-path verification passed for Round1 topic (attach + send + wait_reply_done).
- Three-party `groupchat_round1` rerun completed successfully.
- Output refreshed: `outputs/llm-web-arena/conversation_history.json` (topic + 3 assistant replies).
- `groupchat_round2` was started in this cycle but manually aborted before completion.

## Current Confirmed Milestone
- Round1 is operational end-to-end:
  - browser attach channel works
  - prompt send works
  - reply collection works

## Command Results (this cycle)
- `uv run --active python3 -m web_llm_arena.cli --mode groupchat_round1 --topic "什么问题，人类正在用错误的方式解决？" --config config.example.json` -> exit 0
- `uv run --active python3 -m web_llm_arena.cli --mode groupchat_round2 --config config.example.json` -> started, then manually aborted

## Next Action
- Focused improvements:
  - Improve multi-browser control efficiency (current behavior is effectively serial and needs better parallel orchestration).
  - Refactor 3-round flow to decouple each round for independent debugging and retry.
- New Round2/3 design direction:
  1. Round1 collects replies and writes outputs.
  2. Publish/sync Round1 artifacts to public status repo.
  3. Share the public raw link with each LLM so they read artifacts directly.
  4. Each model returns structured scoring response (Round2), reducing long-text input pressure on web UI textareas.
  5. Continue with Round3 voting on top of persisted/public artifacts.

## Known Blocker
- Web UI input fields are unreliable for very long prompts in Round2, which caused interruption.

## Security
- Public status excludes token/cookie/authorization/secrets.
- Any suspected sensitive string must be replaced with `***REDACTED***`.

## Updated At
- 2026-03-02 21:30 (Asia/Shanghai)

## Raw Link
- https://raw.githubusercontent.com/laity2010/llm-project-build-status/main/outbox/llm-web-arena/STATUS.md
