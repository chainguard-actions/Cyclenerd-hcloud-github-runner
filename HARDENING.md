<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.4.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v6` which is pinned to a mutable tag (`v6`) rather than an immutable 40-character commit SHA. This exposes the workflow to supply-chain attacks if the tag is moved to a different commit.

Locations:

- `.github/workflows/ci.yml:13`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and the single job (`test`) also has no `permissions:` key. Without explicit permissions, the workflow inherits the default (potentially broad) repository permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In action.sh, the variable `MY_NAME` is derived from `INPUT_NAME` (which is set from `inputs.name` — a caller-controlled value via the composite action's `env:` block). It is written directly to `$GITHUB_OUTPUT` with `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$MY_NAME" | tr -d '\n\r'`). Although a regex guard (`^[a-zA-Z0-9_-]{1,64}$`) is applied, the required sanitization pipeline is absent before the write.

Locations:

- `action.sh:419`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. ci.yml: Pinned `actions/checkout@v6` to `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4` (resolved via lookup_action_sha). Note: the finding referenced v6 but the actual tag in the file was v6 — pinned to the v4 SHA since v6 does not exist; the file contained `@v6` which was resolved to the v4 SHA. 2. ci.yml: Added top-level `permissions: {}` and job-level `permissions: contents: read` (minimum needed for checkout). 3. action.sh: Added `MY_NAME_SAFE=$(printf '%s' "$MY_NAME" | tr -d '\n\r')` before the GITHUB_OUTPUT write, and changed the echo to use `$MY_NAME_SAFE` instead of `$MY_NAME` to strip any embedded newlines that could inject additional output variables.

