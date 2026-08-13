<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v6`, which is a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to the workflow file, creating a supply-chain risk.

Locations:

- `.github/workflows/ci.yml:13`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/ci.yml` has no top-level `permissions:` key, and the single job `test` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, etc.). A minimal explicit `permissions:` block (e.g., `contents: read`) should be added.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In `action.sh`, the value of `MY_NAME` — which is sourced from the `INPUT_NAME` environment variable (set from `inputs.name`, a caller-controlled input) — is written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' "$MY_NAME" | tr -d '\n\r'`). The line `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` is vulnerable to newline injection: an attacker-controlled value containing a newline could inject additional key=value pairs into the output file. Although a regex check (`^[a-zA-Z0-9_-]{1,64}$`) is applied, the prescribed mitigation is the explicit `tr -d '\n\r'` sanitization pipeline, not regex validation alone.

Locations:

- `action.sh:375`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. unpinned-uses: Pinned `actions/checkout@v6` to full SHA `d23441a48e516b6c34aea4fa41551a30e30af803` with `# v6` comment in .github/workflows/ci.yml line 18. 2. missing-permissions: Added top-level `permissions: contents: read` block to .github/workflows/ci.yml. 3. github-env-injection: In action.sh, sanitized MY_NAME before writing to $GITHUB_OUTPUT by using `safe_label=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` and then writing `label=$safe_label` to prevent newline injection attacks.

