# VAPT Remediation Tool

A local, client-side HTML tool that generates structured context files for AI-assisted VAPT
(Vulnerability Assessment and Penetration Testing) remediation of Java microservices.

It streamlines fixing two classes of security findings flagged by scanners like Wiz:

- **SCA (Software Composition Analysis)** — vulnerable third-party dependencies
- **Hard-coded secrets** — credentials committed directly in source code

> ⚠️ **Security note:** This repository intentionally excludes files that contain live secrets
> (Devtron key dumps, tracker spreadsheets, Wiz CSVs). See [Security](#security) below. Never
> commit those files.

## What Is This?

Remediating dozens of repos by hand is repetitive: each needs a dependency version bump plus
secrets moved to environment variables. This tool captures the per-repo inputs (repo name,
environment ↔ branch mapping, Wiz report, Devtron config) and generates a single context file
you can paste into a fresh AI chat. The AI then reads the repo, identifies vulnerabilities,
applies fixes, and opens a PR.

## How It Works

1. Open `index.html` in your browser.
2. Fill in the form for a repo:
   - **Repo name** — e.g. `org/service-name`
   - **Environments** — env name ↔ git branch mapping (e.g. prod → `master`)
   - **Devtron config** — existing key/value pairs for that environment
   - **Wiz report** — paste or upload the detailed CSV export
3. Click **Generate Context File** and copy/download the output.
4. Paste it into a fresh AI chat session.
5. The AI reads the repo, finds vulnerabilities, applies fixes, creates a PR, and returns the
   Devtron values to configure.

## What Gets Fixed

### SCA (vulnerable dependencies)

Transitive dependency version overrides in `pom.xml`. For example, overriding the embedded
Tomcat version to remediate known CVEs:

```xml
<properties>
    <tomcat.version>9.0.100</tomcat.version>
</properties>
```

### Hard-coded secrets

Secrets are replaced with environment-variable placeholders and injected at runtime:

| Secret type        | Placeholder                          | Resolved by                    |
| ------------------ | ------------------------------------ | ------------------------------ |
| DB password        | `${WMS_DB_PASSWORD}`                 | Spring Boot property resolution|
| JWT / service token| `${FAAS_SERVICE_TOKEN}`              | Spring Boot property resolution|
| API key            | `${WMS_REQ_TRACKER_API_KEY}`         | Spring Boot property resolution|
| New Relic license  | `<%= ENV["NEW_RELIC_LICENSE_KEY"] %>`| New Relic agent (ERB)          |
| Sentry DSN         | `${SENTRY_DSN}`                      | Spring Boot property resolution|

## Project Structure

```
vapt-tool/
├── index.html                    # The form (open in a browser)
├── README.md                     # This file
├── SETUP.md                      # Setup & usage guide
├── WINDOWS.md                    # Windows-specific setup & usage guide
├── VAPT_REMEDIATION_ACTIVITY.md  # Full remediation methodology & tracking
├── VAPT_TOMCAT_UPDATE_REPORT.md  # Example remediation report
└── .gitignore                    # Excludes secret files from version control
```

## Getting Started

No build step, server, or dependencies required — it is pure client-side HTML/JS.

```bash
# Clone the repo
git clone <repo-url>
cd vapt-tool

# Open the tool in your browser (Linux)
xdg-open index.html
# macOS:  open index.html
# Windows: start index.html
```

See [SETUP.md](SETUP.md) for detailed usage, or [WINDOWS.md](WINDOWS.md) if you're on Windows.

## Security

This tool is designed so **no data leaves your machine** — everything runs client-side.

The following file types are **git-ignored and must never be committed** because they contain
live credentials or sensitive internal data:

- `devtron_qa_keys*.txt` — Devtron secret dumps (DB passwords, JWT/service tokens)
- `*.xlsx` / `*.csv` — tracker spreadsheets and Wiz report exports

If you need to share configuration, share only placeholder names, never real values.

## License

Internal tooling. Not for public distribution.
