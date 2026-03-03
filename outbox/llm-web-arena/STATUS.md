# llm-web-arena / ver01

LAST_UPDATE: 2026-03-03 08:55 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Stabilize ChatGPT chain first, then resume full multi-agent round flow.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: PASS
- Last failure stage: SEND_CLICKED
- Last 10 runs: PASS FAIL FAIL PASS
- Runs today: 4
- Consecutive pass: 1
- Pass rate (last 20): 2/20 (10.0%)
- Most common fail_stage (last 20): INPUT_DONE (1)

## HEALTH_METRICS
- Last 20 runs pass rate: 2/20 (10.0%)
- Consecutive pass: 1
- Failure distribution by fail_stage: INPUT_DONE=1, SEND_CLICKED=1
- Regression detection: NO (none)

## STATE_RULES
- STATE derivation source: run history only (last 20 runs + consecutive pass).
- Manual state selection: FORBIDDEN.
- If pass_rate(last20) >= 95% AND consecutive_pass >= 20 -> STATE=ROUND2_STABLE.
- Else if pass_rate(last20) < 80% -> STATE=ROUND2_BLOCKED.
- Else -> STATE=ROUND2_IN_PROGRESS.

## BLOCKER
- Primary fail_stage: SEND_CLICKED
- Observed symptom: [chatgpt] send failed; diag=debug_screenshots/20260303_084327_chatgpt_send.txt
- Hypothesis: Exactly-once click guard is violated by transient UI behavior.

## REPRO
- Step 1: uv run --active python3 -m web_llm_arena.cli --mode probe_check --ai chatgpt --probe-format json_marked --config config.example.json
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: PASS

## EVIDENCE
- Failing stage: NONE
- Logs:
  - outbox/llm-web-arena/runs/chatgpt_only_20260303_085432/chatgpt_reply.txt
  - outbox/llm-web-arena/runs/chatgpt_only_20260303_085432/chatgpt_round1_single.json
- Screenshots:
  - UNKNOWN
- DOM / selectors (snippet):
  - input candidate: chatgpt:#thread-bottom > div > div > div.pointer-events-auto.relative.z-1.flex.h-\(--composer-container-height\,100\%\).max-w-full.flex-\(--composer-container-flex\,1\).flex-col > form > div:nth-child(2) > div > div.-my-2\.5.flex.min-h-14.items-center.overflow-x-hidden.px-1\.5.\[grid-area\:primary\].group-data-expanded\/composer\:mb-0.group-data-expanded\/composer\:px-2\.5 > div
  - send button: chatgpt:#composer-submit-button:not([disabled])
  - assistant message container: chatgpt:div[data-message-author-role='assistant']
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
  - page_load_sec=30, element_wait_sec=20, reply_done_wait_sec=300, poll_interval_sec=1.0

## EXPERIMENTS (latest first)
### E04 (2026-03-03)
Change:
- Single variable change in automation/selector strategy.
Setup:
- Input size: 18 chars
- Runs: 1
Result:
- Pass rate: 2/4
- Failure mode: PASS
Conclusion:
- Needs follow-up validation.
Next:
- Execute one additional controlled run.

## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
- P1: Enforce exactly-once click token per run_id.
- P2: Capture post-click input state to detect duplicate submissions.
- P3: Verify click fallback order (native -> JS) with deterministic guard.
