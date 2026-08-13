<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.2.0** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. Pinned `actions/checkout@v4` to full SHA `11d5960a326750d5838078e36cf38b85af677262` with `# v4` comment in `.github/workflows/ci.yml`. 2. Added top-level `permissions: contents: read` block to `.github/workflows/ci.yml` to restrict default permissions. 3. In `action.sh`, sanitized `$MY_NAME` and `$MY_HETZNER_SERVER_ID` using `printf '%s' "$VAR" | tr -d '\n\r'` before writing to `$GITHUB_OUTPUT`, preventing newline injection attacks.

