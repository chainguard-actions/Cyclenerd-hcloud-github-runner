<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.3.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow file uses `actions/checkout@v4`, which is pinned to a mutable tag (`v4`) rather than a full 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to the workflow file, creating a supply-chain risk.

Locations:

- `.github/workflows/ci.yml:13`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/ci.yml` has no top-level `permissions:` key and the single job (`test`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents). A minimal `permissions:` block (e.g., `contents: read`) should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

1. Pinned `actions/checkout@v4` to full SHA `11d5960a326750d5838078e36cf38b85af677262` with `# v4` comment for readability. 2. Added top-level `permissions: contents: read` block — the minimum required for a workflow that only checks out code and runs shellcheck.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in action.sh at line 330. Added sanitization step `MY_NAME_SAFE=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` immediately before writing to $GITHUB_OUTPUT, and updated the echo to use the sanitized variable `$MY_NAME_SAFE` instead of the raw `$MY_NAME`. The server_id value was already validated as a pure integer and did not require sanitization.

