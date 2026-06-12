<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Cyclenerd--hcloud-github-runner/v1.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In action.sh (executed by the action.yml run: block), the variable MY_NAME is derived from the untrusted input `inputs.name` (via the INPUT_NAME environment variable set to `${{ inputs.name }}` in action.yml). MY_NAME is written directly to $GITHUB_OUTPUT (`echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"`) without the required sanitization step (`printf '%s' "$MY_NAME" | tr -d '\n\r'`). Although a regex check (`^[a-zA-Z0-9_-]{1,64}$`) is applied, the check requires the specific sanitization pipeline before every write to a special environment file when the source is untrusted input. No `printf`/`tr -d` pattern exists anywhere in action.sh.

Locations:

- `action.sh:430`
- `action.yml:115`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed action.sh at the GITHUB_OUTPUT write for the 'label' output: added `MY_NAME_SAFE=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` before the echo statement, and changed `echo "label=$MY_NAME"` to `echo "label=$MY_NAME_SAFE"`. This sanitizes the untrusted input-derived MY_NAME variable (from inputs.name via INPUT_NAME env var) before writing it to $GITHUB_OUTPUT, preventing newline injection attacks. The server_id output was not changed as it is already validated to be an integer via regex check.

