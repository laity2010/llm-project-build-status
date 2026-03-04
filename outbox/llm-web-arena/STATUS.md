# llm-web-arena / ver01

LAST_UPDATE: 2026-03-04 21:25 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Establish ver01 single-browser serial-tabs Round1/Round2 pipeline with public raw handoff and reproducible artifacts.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: PASS
- Last failure stage: UNKNOWN
- Last 10 runs: FAIL PASS PASS PASS PASS FAIL FAIL PASS PASS PASS
- Runs today: 4
- Consecutive pass: 3
- Pass rate (last 20): 8/20 (40.0%)
- Most common fail_stage (last 20): WAIT_INPUT_READY (4)

## HEALTH_METRICS
- Last 20 runs pass rate: 8/20 (40.0%)
- Consecutive pass: 3
- Failure distribution by fail_stage: WAIT_INPUT_READY=4, SEND_CLICKED=3, UNKNOWN=3, WAIT_STREAM_END=1, WAIT_STREAM_START=1
- Grok blocked_title hit rate (last 20): 0/42 (0.0%)
- Grok stream_start outlier rate >5s (last 20): 5/42 (11.9%)
- Grok focus_issue hit rate (last 20): 1/22 (4.5%)
- Grok verification hit rate (last 20): 0/22 (0.0%)
- Grok focus↔verification overlap (last 20): 0/2 (0.0%)
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
- Step 1: cd ver01 && bash scripts/mac/run_round2_serialtabs.sh
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: PASS

## EVIDENCE
- Failing stage: NONE
- Logs:
  - outbox/llm-web-arena/artifacts/notes/round1-20260304_123404-6d216be5.md
  - outbox/llm-web-arena/artifacts/notes/round2-20260304_130820-01ceca75.md
  - outbox/llm-web-arena/artifacts/notes/round2-20260304_130820-01ceca75_cross_table.csv
  - outbox/llm-web-arena/artifacts/notes/round2-20260304_130820-01ceca75_report.json
- Screenshots:
  - UNKNOWN
- DOM / selectors (snippet):
  - input candidate: ver01/config/config.example.json::sites.*.locators.input
  - send button: ver01/config/config.example.json::sites.*.locators.send
  - assistant message container: ver01/config/config.example.json::sites.*.locators.reply_container
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
  - page_load_sec=30, element_wait_sec=20, reply_done_wait_sec=60, poll_interval_sec=0.5

## EXPERIMENTS (latest first)
### E00 (2026-03-04)
Change:
- Baseline migration snapshot; no new mitigation introduced.
Setup:
- Input size: 145 chars
- Runs: 20
Result:
- Pass rate: 8/20
- Failure mode: UNKNOWN
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
