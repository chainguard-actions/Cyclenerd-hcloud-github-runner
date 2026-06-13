<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Cyclenerd--hcloud-github-runner/v1.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.sh, the variable MY_NAME is derived from INPUT_NAME (which maps to the caller-controlled input `inputs.name`) and is written directly to $GITHUB_OUTPUT without the required sanitization step (`printf '%s' "$MY_NAME" | tr -d '\n\r'`). The line `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` writes an unsanitized, caller-controlled value to the special environment file. Although MY_NAME is validated against `^[a-zA-Z0-9_-]{1,64}$` (which incidentally excludes newlines), the check requires the `printf | tr -d '\n\r'` sanitization pipeline to be applied immediately before every write to $GITHUB_OUTPUT/$GITHUB_ENV/$GITHUB_PATH when the source is untrusted input.

Locations:

- `action.sh:449`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.sh at the line writing 'label=$MY_NAME' to $GITHUB_OUTPUT. Added sanitization: `safe_name=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` and changed the echo to use `$safe_name` instead of `$MY_NAME`. The server_id write was left unchanged as MY_HETZNER_SERVER_ID is already validated as a pure integer.

