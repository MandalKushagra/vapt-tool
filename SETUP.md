# Setup & Usage Guide

This guide walks through running the VAPT Remediation Tool and using its output.

## Prerequisites

- A modern web browser (Chrome, Firefox, Edge, or Safari)
- Access to your scanner (e.g. Wiz) to export per-repo reports
- Access to your deployment platform (e.g. Devtron) to read/write environment secrets

No runtime, package manager, or build step is needed — the tool is a single static HTML file.

## 1. Open the Tool

Clone the repository and open `index.html` directly in a browser:

```bash
git clone <repo-url>
cd vapt-tool
```

Then open the file:

| OS      | Command              |
| ------- | -------------------- |
| Linux   | `xdg-open index.html`|
| macOS   | `open index.html`    |
| Windows | `start index.html`   |

Alternatively, drag `index.html` into a browser window, or serve it locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/index.html
```

## 2. Fill In the Form

For each repository, provide:

- **Repo name** — the full `org/repo` identifier (e.g. `org/service-name`).
- **Environments** — one row per environment, mapping the environment name to its git branch:
  - `prod` → `master`
  - `qa_mumbai` → `qa-`
  - `qa2_mumbai` → `grv_qa2`
- **Devtron config** — paste the existing key/value pairs already configured for that
  environment. This lets the generated context reuse existing env var names.
- **Wiz report** — export the **Detailed CSV** from your scanner and paste or upload it.

## 3. Generate & Copy

Click **Generate Context File**. Review the output, then copy or download it.

## 4. Run the Remediation

Paste the generated context into a fresh AI chat session. The context file includes the full
instructions, so the assistant can:

1. Read `pom.xml` and config files from the repo.
2. Identify vulnerable dependencies from the Wiz report.
3. Find hard-coded secrets across config files.
4. Apply fixes (version overrides + `${ENV_VAR}` placeholders).
5. Create a feature branch and open a PR.
6. Output the Devtron key/value pairs to configure.

## 5. Configure Secrets Before Deploying

> **Important:** Add the secret key/value pairs to your deployment platform (e.g. Devtron
> **Secrets**, not ConfigMap) for each environment **before** deploying the code change.
> Otherwise the app fails to start due to unresolved placeholders.

## Workflow Summary

```
Fill form ──▶ Generate context ──▶ Paste into AI chat ──▶ AI fixes + opens PR
                                                              │
                                        Add secrets to Devtron (before deploy)
                                                              │
                                                    Merge ──▶ Deploy ──▶ Verify
```

## Do Not Commit Secrets

The `.gitignore` excludes secret-bearing files (`*_keys.txt`, `*.xlsx`, `*.csv`). Keep them
local. If you must share config, share placeholder names only — never real values.

For the full remediation methodology, branching strategy, and tracking approach, see
[VAPT_REMEDIATION_ACTIVITY.md](VAPT_REMEDIATION_ACTIVITY.md).
