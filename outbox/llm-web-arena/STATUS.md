# llm-web-arena / ver01

LAST_UPDATE: 2026-03-03 22:05 (Asia/Singapore)
OWNER: codex
STATE: ROUND2_BLOCKED
GOAL: Automate duel run status artifacts and deterministic control-plane rendering.
DEFINITION_OF_DONE:
- >=20 consecutive successful runs
- input accepted correctly
- exactly one send click
- reply captured from LAST assistant message
- JSON parsing success rate >=95%

## CURRENT
- Last run: PASS
- Last failure stage: WAIT_INPUT_READY
- Last 10 runs: FAIL FAIL FAIL FAIL FAIL FAIL PASS PASS PASS PASS
- Runs today: 19
- Consecutive pass: 4
- Pass rate (last 20): 7/20 (35.0%)
- Most common fail_stage (last 20): SEND_CLICKED (4)

## HEALTH_METRICS
- Last 20 runs pass rate: 7/20 (35.0%)
- Consecutive pass: 4
- Failure distribution by fail_stage: SEND_CLICKED=4, WAIT_INPUT_READY=4, UNKNOWN=2, INPUT_DONE=1, WAIT_STREAM_END=1
- Regression detection: NO (none)

## STATE_RULES
- STATE derivation source: run history only (last 20 runs + consecutive pass).
- Manual state selection: FORBIDDEN.
- If pass_rate(last20) >= 95% AND consecutive_pass >= 20 -> STATE=ROUND2_STABLE.
- Else if pass_rate(last20) < 80% -> STATE=ROUND2_BLOCKED.
- Else -> STATE=ROUND2_IN_PROGRESS.

## BLOCKER
- Primary fail_stage: WAIT_INPUT_READY
- Observed symptom: tab_mismatch: expected_handle=7171A95E93AE6C8343EBC500CC0A7A5A current_handle=7171A95E93AE6C8343EBC500CC0A7A5A url=https://grok.com/ title=请稍候… blocked_title=True; diag=debug_screenshots/20260303_160535_grok_assert_active_tab.txt
- Hypothesis: Input element readiness detection is unstable.

## REPRO
- Step 1: env -u http_proxy -u https_proxy -u all_proxy -u HTTP_PROXY -u HTTPS_PROXY -u ALL_PROXY NO_PROXY=127.0.0.1,localhost,::1 no_proxy=127.0.0.1,localhost,::1 uv run --active python3 -m web_llm_arena.cli --mode serialtabs_smoke --start-ai grok --probe-text "serialtabs pre-round1 test" --probe-format json_marked --config config.example.json
- Step 2: Ensure Chrome debug ports are up and authenticated for each target site.
- Expected: run completes and writes artifacts + rendered status.
- Actual: PASS

## EVIDENCE
- Failing stage: NONE
- Logs:
  - outbox/llm-web-arena/artifacts/notes/grok_stage_split_distribution_20260303_220500.json
  - outbox/llm-web-arena/artifacts/notes/grok_stage_split_distribution_20260303_220500.md
  - outbox/llm-web-arena/artifacts/notes/grok_stage_split_distribution_latest.json
- Screenshots:
  - UNKNOWN
- DOM / selectors (snippet):
  - input candidate: chatgpt:#thread-bottom > div > div > div.pointer-events-auto.relative.z-1.flex.h-\(--composer-container-height\,100\%\).max-w-full.flex-\(--composer-container-flex\,1\).flex-col > form > div:nth-child(2) > div > div.-my-2\.5.flex.min-h-14.items-center.overflow-x-hidden.px-1\.5.\[grid-area\:primary\].group-data-expanded\/composer\:mb-0.group-data-expanded\/composer\:px-2\.5 > div | gemini:#app-root chat-window input-area-v2 rich-textarea div.ql-editor[contenteditable='true'][role='textbox'] | grok:body > div.group\/sidebar-wrapper.flex.flex-col.h-svh.w-full.has-\[\[data-variant\=inset\]\]\:bg-sidebar.isolate > div > div.flex.w-full.h-full.overflow-hidden.\@container\/mainview.relative > div > div > main > div.flex.flex-col.items-center.w-full.h-full.p-2.mx-auto.justify-center.\@sm\:p-4.\@sm\:gap-9.isolate.mt-16.\@sm\:mt-0.overflow-scroll > div > div.absolute.bottom-0.mx-auto.inset-x-0.max-w-breakout.\@sm\:relative.flex.flex-col.items-center.w-full.gap-1.\@sm\:gap-5.\@sm\:bottom-auto.\@sm\:inset-x-auto.\@sm\:max-w-full > div > div > form > div > div > div.ps-11.pe-32 > div.relative.z-10 > div > div > div
  - send button: chatgpt:#composer-submit-button:not([disabled]) | gemini:#app-root chat-window input-area-v2 div.send-button-container button:not([disabled]):not([aria-disabled='true']) | grok:body > div.group\/sidebar-wrapper.flex.flex-col.h-svh.w-full.has-\[\[data-variant\=inset\]\]\:bg-sidebar.isolate > div > div.flex.w-full.h-full.overflow-hidden.\@container\/mainview.relative > div > div > main > div.flex.flex-col.items-center.w-full.h-full.p-2.mx-auto.justify-center.\@sm\:p-4.\@sm\:gap-9.isolate.mt-16.\@sm\:mt-0.overflow-scroll > div > div.absolute.bottom-0.mx-auto.inset-x-0.max-w-breakout.\@sm\:relative.flex.flex-col.items-center.w-full.gap-1.\@sm\:gap-5.\@sm\:bottom-auto.\@sm\:inset-x-auto.\@sm\:max-w-full > div > div > form > div > div > div.ps-11.pe-32 > div.flex.absolute.inset-x-0.bottom-0.border-2.border-transparent.max-w-full.p-2.\@\[480px\]\/input\:p-2 > div > div.ms-auto.flex.flex-row.items-end.gap-1 > div:nth-child(3) > button
  - assistant message container: chatgpt:div[data-message-author-role='assistant'] | gemini:message-content div[id^='model-response-message-content'][aria-live='polite'][aria-busy='false'] | grok:div[id^='response-'] div.response-content-markdown
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
### E00 (2026-03-03)
Change:
- Baseline migration snapshot; no new mitigation introduced.
Setup:
- Input size: 26 chars
- Runs: 19
Result:
- Pass rate: 7/19
- Failure mode: SEND_CLICKED: Multi-browser parallel control is unstable; move to single-browser serial tabs baseline.
Conclusion:
- Baseline established for future controlled experiments.
Next:
- Introduce one mitigation variable and compare against E00.

## REJECTED / DO_NOT_REPEAT
- Manual STATUS.md edits — non-deterministic and non-reproducible (2026-03-03)

## NEXT_ACTIONS (ordered)
- P1: Audit writable input locator priority and focus acquisition timing.
- P2: Add retry with explicit interactable check before write.
- P3: Record per-locator hit/miss counts for input candidates.
