<!-- markdownlint-disable -->

# Hardening Report: Cyclenerd--hcloud-github-runner/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Cyclenerd--hcloud-github-runner/v1.4.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. In action.sh, the variable `MY_NAME` is derived from `INPUT_NAME` (which is set from `inputs.name` — a caller-controlled value via the composite action's `env:` block). It is written directly to `$GITHUB_OUTPUT` with `echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$MY_NAME" | tr -d '\n\r'`). Although a regex guard (`^[a-zA-Z0-9_-]{1,64}$`) is applied, the required sanitization pipeline is absent before the write.

Locations:

- `action.sh:419`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.sh without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.sh b/action.sh
index a3dd6d5..dadc026 100644
--- a/action.sh
+++ b/action.sh
@@ -457,7 +457,8 @@ fi
 # https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands/
 #echo "::set-output name=label::$MY_NAME"
 #echo "::set-output name=server_id::$MY_HETZNER_SERVER_ID"
-echo "label=$MY_NAME" >> "$GITHUB_OUTPUT"
+MY_NAME_SAFE=$(printf '%s' "$MY_NAME" | tr -d '\n\r')
+echo "label=$MY_NAME_SAFE" >> "$GITHUB_OUTPUT"
 echo "server_id=$MY_HETZNER_SERVER_ID" >> "$GITHUB_OUTPUT"
 
 # Wait for server

```

Locations:

- `action.sh`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index b156ec5..95369c6 100644
--- a/action.yml
+++ b/action.yml
@@ -1,11 +1,9 @@
 name: Self-Hosted GitHub Actions Runner on Hetzner Cloud
 description: A GitHub Action to automatically create Hetzner Cloud servers and register them as self-hosted GitHub Actions runners.
 author: Nils Knieling
-
 branding:
   icon: 'server'
   color: 'red'
-
 inputs:
   create_wait:
     description: >-
@@ -19,8 +17,7 @@ inputs:
     default: '360'
   enable_ipv4:
     description: >-
-      Attach an IPv4 on the public NIC (true/false) . If false, no IPv4 address will be attached.
-      Warning: The GitHub API requires IPv4. Disabling it will result in connection failures.
+      Attach an IPv4 on the public NIC (true/false) . If false, no IPv4 address will be attached. Warning: The GitHub API requires IPv4. Disabling it will result in connection failures.
     required: false
     default: 'true'
   enable_ipv6:
@@ -61,19 +58,16 @@ inputs:
     default: 'null'
   pre_runner_script:
     description: >-
-      Specifies bash commands to run before the GitHub Actions Runner starts.
-      It's useful for installing dependencies with apt-get, dnf, zypper etc.
+      Specifies bash commands to run before the GitHub Actions Runner starts. It's useful for installing dependencies with apt-get, dnf, zypper etc.
     required: false
   primary_ipv4:
     description: >-
-      ID (integer) of the IPv4 Primary IP to use.
-      If omitted and 'enable_ipv4' is true, a new IPv4 Primary IP will automatically be created.
+      ID (integer) of the IPv4 Primary IP to use. If omitted and 'enable_ipv4' is true, a new IPv4 Primary IP will automatically be created.
     required: false
     default: 'null'
   primary_ipv6:
     description: >-
-      ID (integer) of the IPv6 Primary IP to use.
-      If omitted and 'enable_ipv6' is true, a new IPv6 Primary IP will automatically be created.
+      ID (integer) of the IPv6 Primary IP to use. If omitted and 'enable_ipv6' is true, a new IPv6 Primary IP will automatically be created.
     required: false
     default: 'null'
   runner_dir:
@@ -88,9 +82,7 @@ inputs:
     default: '60'
   runner_version:
     description: >-
-      GitHub Actions Runner version (omit 'v'; e.g., '2.321.0').
-      'latest' will install the latest version.
-      'skip' will skip the installation. A working installation is expected in the 'runner_dir'.
+      GitHub Actions Runner version (omit 'v'; e.g., '2.321.0'). 'latest' will install the latest version. 'skip' will skip the installation. A working installation is expected in the 'runner_dir'.
     required: false
     default: 'latest'
   server_id:
@@ -117,21 +109,35 @@ inputs:
       Comma separated Volume IDs (integer) which should be attached to the Server at the creation time.
     required: false
     default: 'null'
-
 outputs:
   label:
     description: >-
-      This label uniquely identifies a GitHub Actions runner,
-      used both to specify which runner a job should execute on via the `runs-on` property and to delete the runner when it's no longer needed.
+      This label uniquely identifies a GitHub Actions runner, used both to specify which runner a job should execute on via the `runs-on` property and to delete the runner when it's no longer needed.
     value: ${{ steps.hcloud-github-runner.outputs.label }}
   server_id:
     description: >-
       This is the Hetzner Cloud server ID of the runner, used to delete the server when the runner is no longer required.
     value: ${{ steps.hcloud-github-runner.outputs.server_id }}
-
 runs:
   using: "composite"
   steps:
     - name: GitHub Actions Runner in Hetzner Cloud
       id: hcloud-github-runner
       shell: bash

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, prior-fix

**Notes:**

action.sh: put the label value through printf | tr -d '\n\r' and stored it in MY_NAME_SAFE before writing it to $GITHUB_OUTPUT. This re-applies the earlier fix. The earlier action.yml patch only joined multi-line input and output descriptions onto one line and removed blank lines. It changed nothing about security, and action.yml already passes every input through env: with no ${{ }} inside run:, so I didn't re-apply it.

