# Grok Stage-Split Bottleneck Distribution (10x serialtabs_smoke)

- Generated (UTC): 2026-03-03T14:05:00.658334+00:00
- Sample size: 10
- Mode: `serialtabs_smoke --start-ai grok`

## Summary
- trigger_to_click_accepted_ms: n=10 min=345.0 p50=501.5 p90=670.7 max=677.0 mean=520.3
- click_accepted_to_stream_started_ms: n=10 min=41.0 p50=202.0 p90=2881.2 max=25050.0 mean=2687.6
- click_accepted_to_stream_started_ms_without_gt_5s: n=9 min=41.0 p50=107.0 p90=368.4 max=418.0 mean=202.889

## Stuck Runs
- NONE

## Per-Run

| file | trigger->click_accepted(ms) | click_accepted->stream_started(ms) | stuck_stage |
|---|---:|---:|---|
| smoke-20260303_134422-f96bf18c.json | 598.0 | 41.0 | NONE |
| smoke-20260303_134606-d48da76c.json | 445.0 | 347.0 | NONE |
| smoke-20260303_134747-ef51f89d.json | 546.0 | 103.0 | NONE |
| smoke-20260303_134932-eea121d8.json | 677.0 | 72.0 | NONE |
| smoke-20260303_135114-caba153c.json | 411.0 | 418.0 | NONE |
| smoke-20260303_135256-ce73a876.json | 631.0 | 85.0 | NONE |
| smoke-20260303_135435-94d329c1.json | 345.0 | 297.0 | NONE |
| smoke-20260303_135614-31e86163.json | 670.0 | 107.0 | NONE |
| smoke-20260303_135756-e1a3836c.json | 423.0 | 356.0 | NONE |
| smoke-20260303_135936-ee34a581.json | 457.0 | 25050.0 | NONE |
