# Grok Smoke 20x (Focus Guard Batch)

- Sample window: last 20 `serialtabs_smoke --start-ai grok` runs
- pass_rate: 13/20
- focus_issue_hit_rate: 1/20
- verification_hit_rate: 0/20
- top_fail_stage: UNKNOWN

## Stage Distribution
- UNKNOWN: 4
- STREAM_STARTED: 2
- WAIT_INPUT_READY: 1

## Error Buckets
- SEND_FAILED_OTHER: 7

## Observation
- Stability improved versus previous 20-run batch (5/20 -> 13/20).
- Focus-related hits exist but low frequency (1/20).
- Verification-title signal not triggered in this sample (0/20); residual failures remain mostly unclassified send failures.
