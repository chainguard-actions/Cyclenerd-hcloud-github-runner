<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Cyclenerd--hcloud-github-runner/v1.3.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.sh, the value `MY_NAME` (derived from `INPUT_NAME`, which maps to the caller-controlled input `inputs.name`) is written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). Although a regex validation (`^[a-zA-Z0-9_-]{1,64}$`) is applied to MY_NAME before the write, the PASS criterion requires the explicit sanitization pipeline — validation alone is not sufficient. The offending line is: `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"`.

Locations:

- `action.sh:418`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in action.sh at line 418. Replaced the direct `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` with an explicit sanitization step: `safe_label=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` followed by `echo "label=$safe_label" >> "$GITHUB_OUTPUT"`. This satisfies the PASS criterion requiring the explicit `printf '%s' ... | tr -d '\n\r'` sanitization pipeline before writing caller-controlled values to $GITHUB_OUTPUT.

