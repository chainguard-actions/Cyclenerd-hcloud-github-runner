<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v4`, which is a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to the workflow file, creating a supply-chain risk.

Locations:

- `.github/workflows/ci.yml:13`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key, and the `test` job also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions (which may include write access to contents, packages, etc.), violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both findings in hardened/action/.github/workflows/ci.yml: (1) Pinned `actions/checkout@v4` to full commit SHA `34e114876b0b11c390a56381ad16ebd13914f8d5` with `# v4` comment for readability. (2) Added top-level `permissions: contents: read` block — the minimum privilege needed for the checkout step; the shellcheck run step requires no additional permissions.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed action.sh line 338: Added sanitization of MY_NAME before writing to $GITHUB_OUTPUT. Introduced MY_NAME_SAFE=$(printf '%s' "$MY_NAME" | tr -d '\n\r') and replaced `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` with `echo "label=$MY_NAME_SAFE" >> "$GITHUB_OUTPUT"`. This strips any newline/carriage-return characters from the untrusted input before it is written to the special GitHub environment file, preventing output injection attacks.

