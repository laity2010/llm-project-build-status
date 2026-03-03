# Grok Send-Confirm Bottleneck Observation

- Timestamp (SGT): 2026-03-03
- Source report: `outputs/llm-web-arena/serialtabs_smoke/smoke-20260303_131412-7f1f2b56.json`
- Mode: `serialtabs_smoke` (single browser, 3 tabs, serial)

## Timing Table

| AI | trigger->confirmed(ms) | send_done->match(ms) | reply_wait_sec | elapsed_sec |
|---|---:|---:|---:|---:|
| grok | 61759.000 | 36.000 | 0.036 | 63.855 |
| chatgpt | 539.000 | 3801.000 | 3.801 | 5.239 |
| gemini | 412.000 | 3247.000 | 3.246 | 4.465 |

## Key Finding

- Bottleneck is concentrated in Grok `trigger->confirmed` (send-confirm stage), not in reply recognition.
- Grok reply detection is fast once send is confirmed (`send_done->match` ~= 36ms).
- Practical implication: latency is dominated by Grok pre-reply submit confirmation path (turnstile/focus/re-render sensitivity), not parser speed.

## Code Pointers

- `src/web_llm_arena/adapters/serial_tabs.py` (`SerialTabsGrokAdapter.send`)
- `src/web_llm_arena/adapters/base.py` (`_safe_get_input_text` fallback path)
- `src/web_llm_arena/resolver.py` (`find_first` per-locator wait behavior)
