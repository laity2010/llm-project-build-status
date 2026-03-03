# llm-web-arena / ver01

LAST_UPDATE: 2026-03-03 07:30 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Make duel automation round2 stable and reproducible under long-input constraints.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: FAIL
- Last failure stage: INPUT_DONE
- Last 10 runs: PASS FAIL
- Runs today: 2
- Consecutive pass: 0
- Pass rate (last 20): 1/20 (5.0%)
- Most common fail_stage (last 20): INPUT_DONE (1)

## HEALTH_METRICS
- Last 20 runs pass rate: 1/20 (5.0%)
- Consecutive pass: 0
- Failure distribution by fail_stage: INPUT_DONE=1
- Regression detection: YES (pass->fail transition; new fail_stage appeared)

## STATE_RULES
- STATE derivation source: run history only (last 20 runs + consecutive pass).
- Manual state selection: FORBIDDEN.
- If pass_rate(last20) >= 95% AND consecutive_pass >= 20 -> STATE=ROUND2_STABLE.
- Else if pass_rate(last20) < 80% -> STATE=ROUND2_BLOCKED.
- Else -> STATE=ROUND2_IN_PROGRESS.

## BLOCKER
- Primary fail_stage: INPUT_DONE
- Observed symptom: Round2 long prompt not reliably accepted by web input field; run aborted for redesign.
- Hypothesis: Input write/read-back path is inconsistent under current UI state.

## REPRO
- Step 1: uv run --active python3 -m web_llm_arena.cli --mode groupchat_round2 --config config.example.json
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: FAIL at INPUT_DONE (Round2 long prompt not reliably accepted by web input field; run aborted for redesign.)

## EVIDENCE
- Failing stage: INPUT_DONE
- Logs:
  - debug_screenshots/20260302_192807_gemini_wait_reply_done.txt
  - outputs/llm-web-arena/conversation_history.json
- Screenshots:
  - debug_screenshots/20260302_192807_gemini_wait_reply_done.png
- DOM / selectors (snippet):
  - input candidate: chatgpt:#prompt-textarea | gemini:#app-root chat-window input-area-v2 rich-textarea div.ql-editor[contenteditable=true] | grok:[contenteditable=true]
  - send button: chatgpt:#composer-submit-button[data-testid=send-button] | gemini:div.send-button-container button:not([disabled]) | grok:button[type=submit][aria-label=提交]
  - assistant message container: chatgpt:article[data-testid] | gemini:message-content .markdown | grok:article
- Code pointers:
  - src/web_llm_arena/cli.py
  - src/web_llm_arena/status_updater.py
  - src/web_llm_arena/git_autopush.py

## STATE_MACHINE
- S0 WAIT_INPUT_READY:
  - Condition: writable input resolved; element visible and enabled.
- S1 INPUT_DONE:
  - Condition: input text read-back equals intended payload.
- S2 WAIT_SEND_READY:
  - Condition: send control visible and enabled; not voice/stop control.
- S3 SEND_CLICKED:
  - Condition: send click executed exactly once per run.
- S4 WAIT_STREAM_START:
  - Condition: assistant stream starts or reply container signature changes.
- S5 WAIT_STREAM_END:
  - Condition: reply text stable across the stability window.
- S6 CAPTURE_REPLY:
  - Condition: last assistant reply captured and post-processed.
- Timeouts:
  - page_load_sec=30, element_wait_sec=20, reply_done_wait_sec=120, poll_interval_sec=1.0

## EXPERIMENTS (latest first)
### E02 (2026-03-03)
Change:
- Single variable change in automation/selector strategy.
Setup:
- Input size: 1266 chars
- Runs: 1
Result:
- Pass rate: 1/2
- Failure mode: INPUT_DONE (Round2 long prompt not reliably accepted by web input field; run aborted for redesign.)
Conclusion:
- Needs follow-up validation.
Next:
- Execute one additional controlled run.

## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
- P1: Validate read-back equality after write and before submit.
- P2: Normalize long-text input path to single write strategy.
- P3: Capture DOM snapshot when read-back mismatches expected text.
