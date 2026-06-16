<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **Cyclenerd--hcloud-github-runner/v1.4.1** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.sh: replaced `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` with a sanitized version that first strips newlines using `safe_label=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` and then writes `echo "label=$safe_label" >> "$GITHUB_OUTPUT"`. The `server_id` output was left unchanged as `MY_HETZNER_SERVER_ID` is already validated to be a pure integer. No changes were needed to action.yml.

