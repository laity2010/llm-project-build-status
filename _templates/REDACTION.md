# REDACTION Rules

## Must Redact
- API keys, passwords, secrets, and private credentials.
- token/cookie values and any auth session material.
- Request/response fragments containing sensitive identifiers.

## Replacement Format
- Replace suspicious strings with `***REDACTED***`.

## Public Repo Guardrails
- Do not store raw sensitive data in markdown, logs, screenshots, or code snippets.
- Keep only minimal sanitized summaries in `reports/`.
