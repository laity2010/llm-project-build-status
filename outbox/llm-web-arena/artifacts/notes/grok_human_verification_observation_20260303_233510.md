# Grok Stability Observation (20x serialtabs_smoke)

- Sample window: last 20 smoke runs (`--start-ai grok`)
- Pass rate: 5/20
- WAIT_INPUT_READY failures: 0
- blocked_title hit rate: 0/20
- stream_start outlier >5s rate: 0/20

## Failure distribution (grok)
- SEND_FAILED_OTHER: 15
- OK: 5

## Stuck stage distribution
- STREAM_STARTED: 8
- UNKNOWN: 7
- NONE: 5

## Operator observation
- User observed Grok starts to trigger human verification after several rounds.
- ChatGPT occasionally shows stream interruption in page UI; Gemini remains stable baseline.
- Current blocker focus: Grok WAIT_STREAM_START stability under anti-bot challenge dynamics.
