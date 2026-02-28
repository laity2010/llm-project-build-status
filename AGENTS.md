# AGENTS Protocol (Control Plane)

## Roles
- `inbox/`: instruction input channel.
- `outbox/`: execution status and progress output channel.
- `reports/`: sanitized summaries only.
- `history/`: archived task/status snapshots.

## Collaboration Rules
- Inbox = instruction source of truth.
- Outbox = execution feedback source of truth.
- Execute in small steps and update status on every meaningful change.
- Keep task and status files short, structured, and timestamped.

## Safety Rules (Mandatory)
- Never disclose secrets, credentials, or session artifacts.
- Never commit token/cookie values.
- Never commit request headers, raw auth material, or full page source dumps.
- Any suspicious sensitive string must be replaced with `***REDACTED***`.
