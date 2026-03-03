# llm-web-arena / ver03

LAST_UPDATE: 2026-03-03 23:35 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Stabilize single-browser serial-tabs execution, prioritizing Grok anti-bot tolerance and stream-start detection determinism.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: FAIL
- Last failure stage: WAIT_STREAM_START
- Last 10 runs: FAIL FAIL FAIL FAIL FAIL PASS PASS PASS PASS FAIL
- Runs today: 20
- Consecutive pass: 0
- Pass rate (last 20): 7/20 (35.0%)
- Most common fail_stage (last 20): SEND_CLICKED (4)

## HEALTH_METRICS
- Last 20 runs pass rate: 7/20 (35.0%)
- Consecutive pass: 0
- Failure distribution by fail_stage: SEND_CLICKED=4, WAIT_INPUT_READY=4, UNKNOWN=2, INPUT_DONE=1, WAIT_STREAM_END=1, WAIT_STREAM_START=1
- Grok blocked_title hit rate (last 20): 0/20 (0.0%)
- Grok stream_start outlier rate >5s (last 20): 0/20 (0.0%)
- Regression detection: YES (pass->fail transition; new fail_stage appeared)

## STATE_RULES
- STATE derivation source: run history only (last 20 runs + consecutive pass).
- Manual state selection: FORBIDDEN.
- If pass_rate(last20) >= 95% AND consecutive_pass >= 20 -> STATE=ROUND2_STABLE.
- Else if pass_rate(last20) < 80% -> STATE=ROUND2_BLOCKED.
- Else -> STATE=ROUND2_IN_PROGRESS.

## BLOCKER
- Primary fail_stage: WAIT_STREAM_START
- Observed symptom: grok serialtabs smoke degraded after repeated rounds: pass_rate=5/20; operator observed human-verification popups after several rounds; dominant_stuck=STREAM_STARTED
- Hypothesis: Stream-start detection misses first assistant transition.

## REPRO
- Step 1: uv run --active python3 -m web_llm_arena.cli --mode serialtabs_smoke --start-ai grok --probe-text "serialtabs pre-round1 test" --probe-format json_marked --config config.example.json
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: FAIL at WAIT_STREAM_START (grok serialtabs smoke degraded after repeated rounds: pass_rate=5/20; operator observed human-verification popups after several rounds; dominant_stuck=STREAM_STARTED)

## EVIDENCE
- Failing stage: WAIT_STREAM_START
- Logs:
  - [grok] send failed
  - debug_screenshots/20260303_232448_grok_send.txt
  - debug_screenshots/20260303_232449_grok_serialtabs_smoke.txt
  - debug_screenshots/20260303_232734_grok_send.txt
  - debug_screenshots/20260303_232734_grok_serialtabs_smoke.txt
  - outbox/llm-web-arena/artifacts/notes/grok_human_verification_observation_20260303_233510.md
- Screenshots:
  - UNKNOWN
- DOM / selectors (snippet):
  - input candidate: serialtabs: config locators
  - send button: serialtabs: config locators
  - assistant message container: serialtabs: config locators
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
### E20 (2026-03-03)
Change:
- Single variable change in automation/selector strategy.
Setup:
- Input size: 0 chars
- Runs: 1
Result:
- Pass rate: 7/20
- Failure mode: WAIT_STREAM_START (grok serialtabs smoke degraded after repeated rounds: pass_rate=5/20; operator observed human-verification popups after several rounds; dominant_stuck=STREAM_STARTED)
Conclusion:
- Needs follow-up validation.
Next:
- Execute one additional controlled run.

## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
- P1: Improve stream-start detection using container signature delta.
- P2: Add early assistant-node appearance probe with short interval.
- P3: Persist first-stream timestamp for timing diagnostics.
