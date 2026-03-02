# llm-web-arena / ver01

LAST_UPDATE: 2026-03-03 07:16 (Asia/Singapore)
OWNER: codex
STATE: ROUND1_WORKING
GOAL: Automate duel run status artifacts and deterministic control-plane rendering.
DEFINITION_OF_DONE:
- Round1/2/3 each produce expected output artifact in attached-browser mode.
- STATUS.md is rendered from artifacts and auto-pushed on change.

## CURRENT
- Last run: PASS
- Runs today: 1
- Consecutive pass: 1
- Pass rate (last 20): 1/20
- Most common fail_stage (last 20): NONE (0)

## BLOCKER
- Symptom:
  - No active blocker.
- Hypothesis:
  - UNKNOWN
- Constraints:
  - UNKNOWN

## REPRO
1. uv run --active python3 -m web_llm_arena.cli --mode groupchat_round1 --topic "sample" --config config.example.json
2. Ensure Chrome debug ports are up and authenticated for each target site.
3. Expected: run completes and writes artifacts + rendered status.
4. Actual: PASS

## EVIDENCE
- Logs:
  - outputs/llm-web-arena/conversation_history.json
- Screenshots:
  - UNKNOWN
- DOM / selectors (snippet):
  - input candidate: chatgpt:#prompt-textarea | gemini:.ql-editor | grok:[contenteditable=true]
  - send button: chatgpt:#composer-submit-button | gemini:button[aria-label="发送"] | grok:button[type=submit]
  - assistant message container: chatgpt:article[data-testid] | gemini:message-content | grok:article
- Code pointers:
  - src/web_llm_arena/cli.py
  - src/web_llm_arena/status_updater.py
  - src/web_llm_arena/git_autopush.py

## STATE_MACHINE
- S0 WAIT_INPUT_READY:
  - Condition: writable input element resolved and interactable.
- S1 INPUT_DONE:
  - Condition: input text exists and read-back matches expected payload.
- S2 WAIT_SEND_READY:
  - Condition: send control is visible/enabled and not voice/stop control.
- S3 SEND_CLICKED:
  - Guard: exactly-once click guard (single submit per run step).
- S4 WAIT_STREAM_START:
  - Condition: assistant reply stream starts or reply container changes.
- S5 WAIT_STREAM_END:
  - Condition: stream output stabilizes over a polling window.
- S6 CAPTURE_REPLY:
  - Condition: final assistant reply extracted and validated.
- Timeouts:
  - page_load_sec=30, element_wait_sec=20, reply_done_wait_sec=120, poll_interval_sec=1.0

## EXPERIMENTS (latest first)


## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
1. Execute round2 and persist scoring artifact JSON.
2. Validate fail_stage transitions for long-input paths.
3. Verify auto git commit/push behavior on status diff.
