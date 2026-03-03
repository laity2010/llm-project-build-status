# llm-web-arena / ver03

LAST_UPDATE: 2026-03-04 07:14 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Stabilize Grok serial-tabs execution under focus drift and anti-bot verification dynamics.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: FAIL
- Last failure stage: UNKNOWN
- Last 10 runs: FAIL FAIL FAIL FAIL PASS PASS PASS PASS FAIL FAIL
- Runs today: 1
- Consecutive pass: 0
- Pass rate (last 20): 6/20 (30.0%)
- Most common fail_stage (last 20): SEND_CLICKED (4)

## HEALTH_METRICS
- Last 20 runs pass rate: 6/20 (30.0%)
- Consecutive pass: 0
- Failure distribution by fail_stage: SEND_CLICKED=4, WAIT_INPUT_READY=4, UNKNOWN=3, INPUT_DONE=1, WAIT_STREAM_END=1, WAIT_STREAM_START=1
- Grok blocked_title hit rate (last 20): 0/40 (0.0%)
- Grok stream_start outlier rate >5s (last 20): 3/40 (7.5%)
- Grok focus_issue hit rate (last 20): 1/20 (5.0%)
- Grok verification hit rate (last 20): 0/20 (0.0%)
- Grok focus↔verification overlap (last 20): N/A (no verification samples)
- Regression detection: NO (none)

## STATE_RULES
- STATE derivation source: run history only (last 20 runs + consecutive pass).
- Manual state selection: FORBIDDEN.
- If pass_rate(last20) >= 95% AND consecutive_pass >= 20 -> STATE=ROUND2_STABLE.
- Else if pass_rate(last20) < 80% -> STATE=ROUND2_BLOCKED.
- Else -> STATE=ROUND2_IN_PROGRESS.

## BLOCKER
- Primary fail_stage: UNKNOWN
- Observed symptom: grok smoke batch20: pass_rate=13/20; focus_issue_hit_rate=1/20; verification_hit_rate=0/20; top_fail_stage=UNKNOWN; error_buckets={'SEND_FAILED_OTHER': 7}
- Hypothesis: UNKNOWN

## REPRO
- Step 1: uv run --active python3 -m web_llm_arena.cli --mode serialtabs_smoke --start-ai grok --probe-text "serialtabs pre-round1 test" --probe-format json_marked --config config.example.json
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: FAIL at UNKNOWN (grok smoke batch20: pass_rate=13/20; focus_issue_hit_rate=1/20; verification_hit_rate=0/20; top_fail_stage=UNKNOWN; error_buckets={'SEND_FAILED_OTHER': 7})

## EVIDENCE
- Failing stage: UNKNOWN
- Logs:
  - debug_screenshots/20260304_000605_grok_send.txt
  - debug_screenshots/20260304_000605_grok_serialtabs_smoke.txt
  - debug_screenshots/20260304_001136_grok_send.txt
  - debug_screenshots/20260304_001136_grok_serialtabs_smoke.txt
  - debug_screenshots/20260304_001332_grok_send.txt
  - debug_screenshots/20260304_001332_grok_serialtabs_smoke.txt
  - debug_screenshots/20260304_001603_grok_send.txt
  - debug_screenshots/20260304_001604_grok_serialtabs_smoke.txt
  - outbox/llm-web-arena/artifacts/notes/grok_focus_guard_batch20_20260304_071449.md
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
### E00 (2026-03-04)
Change:
- Baseline migration snapshot; no new mitigation introduced.
Setup:
- Input size: 0 chars
- Runs: 20
Result:
- Pass rate: 6/20
- Failure mode: UNKNOWN: grok smoke batch20: pass_rate=13/20; focus_issue_hit_rate=1/20; verification_hit_rate=0/20; top_fail_stage=UNKNOWN; error_buckets={'SEND_FAILED_OTHER': 7}
Conclusion:
- Baseline established for future controlled experiments.
Next:
- Introduce one mitigation variable and compare against E00.

## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
- P1: Investigate UNKNOWN failure context: grok smoke batch20: pass_rate=13/20; focus_issue_hit_rate=1/20; verification_hit_rate=0/20; top_fail_stage=UNKNOWN; error_buckets={'SEND_FAILED_OTHER': 7}
- P2: Add targeted instrumentation for next failing stage resolution.
- P3: Re-run controlled reproduction with fixed input size.
