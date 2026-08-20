# VAPT Remediation Activity — WMS Team

## Overview

As part of Delhivery's security program, the Cybersecurity team has identified **High and Critical severity SDLC vulnerabilities** across multiple GitHub repositories. These findings were detected via **Wiz** and include:

1. **Hard-Coded Secrets** — Private credentials (DB passwords, API keys, JWT tokens) committed directly in source code
2. **SCA (Software Composition Analysis)** — Vulnerable third-party libraries/packages with known exploits

**Deadline: 30 June 2026**  
Any extension requires CTO approval over email.

---

## Scope

**Team:** WMS (Warehouse Management System)  
**Repos assigned:** 29 repositories  
**Authors:** luckydelhivery, devendraratnam, THE-MKR-DELHIVERY (himanshu.arora2)

### Repositories

| # | Repo | Category |
|---|------|----------|
| 1 | delhivery/wms-container | WMS Core |
| 2 | delhivery/wms-pick | WMS Core |
| 3 | delhivery/wms-pack | WMS Core |
| 4 | delhivery/wms-eye | WMS Core |
| 5 | delhivery/wms-amplifier | WMS Core |
| 6 | delhivery/wms-order-request | WMS Core |
| 7 | delhivery/wms-platform-integrator | WMS Core |
| 8 | delhivery/wms-request-fetcher | WMS Core |
| 9 | delhivery/wms-sdp | WMS Core |
| 10 | delhivery/wms-notification-retry-service | WMS Core |
| 11 | delhivery/wms-gim | WMS Core |
| 12 | delhivery/wms-workflow-rule-engine | WMS Core |
| 13 | delhivery/wms-user-management | WMS Core |
| 14 | delhivery/wms-data-dev | WMS Core |
| 15 | delhivery/phuze | Phuze |
| 16 | delhivery/phuze-client-service | Phuze |
| 17 | delhivery/telescope-order-amplifier | Telescope |
| 18 | delhivery/telescope-order-version2 | Telescope |
| 19 | delhivery/telescope-order-db-writer | Telescope |
| 20 | delhivery/telescope-communications | Telescope |
| 21 | delhivery/telescope_version2 | Telescope |
| 22 | delhivery/telescope_db_writer | Telescope |
| 23 | delhivery/telescope-permission-version2 | Telescope |
| 24 | delhivery/telescope-data-sync | Telescope |
| 25 | delhivery/telescope_amplifier | Telescope |
| 26 | delhivery/gm-amazon-fetcher | GM |
| 27 | delhivery/gm-amz-mlts-service | GM |
| 28 | delhivery/e2e-transport-selection | E2E |
| 29 | delhivery/e2e-order-management | E2E |

---

## What Was Found

### SCA Findings (Example: wms-container)

| CVE | Severity | Component | Current Version | Fix Version |
|-----|----------|-----------|----------------|-------------|
| CVE-2023-44487 | High | org.apache.tomcat.embed:tomcat-embed-core | 9.0.69 | ≥ 9.0.81 |
| CVE-2025-24813 | Critical | org.apache.tomcat.embed:tomcat-embed-core | 9.0.69 | ≥ 9.0.99 |

**Root cause:** Spring Boot 2.7.6 bundles Tomcat 9.0.69 as a transitive dependency via `spring-boot-starter-web`. This version has publicly exploited vulnerabilities.

### Secrets Findings (Common across repos)

| Secret Type | Where Found | Risk |
|-------------|-------------|------|
| RDS Database passwords | application-prod.properties, application-qa_mumbai.properties | Unauthorized DB access |
| JWT tokens (FAAS) | application.properties, application-prod.properties | Service impersonation |
| New Relic license key | newrelic.yml | Monitoring data exposure |
| Sentry DSN | sentry.properties | Error tracking data exposure |
| API keys | application.properties | Unauthorized API access |

---

## Remediation Approach

### SCA Fix

**Method:** Override the vulnerable transitive dependency version in `pom.xml`

```xml
<properties>
    <tomcat.version>9.0.100</tomcat.version>
</properties>
```

**Why this works:**
- Spring Boot's parent POM defines `<tomcat.version>` which controls the embedded Tomcat version
- Child projects can override this property to pull a newer version
- No code changes required — purely a dependency resolution change
- Tomcat 9.0.100 is backward compatible with 9.0.69 (same Servlet 4.0 API)

**Version selection criteria:**
- Must be ≥ 9.0.99 (to fix both CVEs)
- Should be 2-3 months old (proven stable)
- Tomcat 9.0.x is supported until March 2027
- Selected: **9.0.100** (released Feb 2025, stable)

### Secrets Fix

**Method:** Replace hardcoded values with environment variable placeholders, inject via Devtron at runtime

| File Type | Placeholder Syntax | Resolved By |
|-----------|-------------------|-------------|
| `application-*.properties` | `${ENV_VAR_NAME}` | Spring Boot property resolution |
| `sentry.properties` | `${ENV_VAR_NAME}` | Spring Boot property resolution |
| `newrelic.yml` | `<%= ENV["ENV_VAR_NAME"] %>` | New Relic agent ERB parser |

**Devtron setup:** Add the secret values as Devtron Secrets (not ConfigMap) per environment. Devtron injects them as environment variables into the K8s pod at runtime.

---

## Branching & Deployment Strategy

### Branch Structure
```
master          → Prod deployment
stg_master      → Staging
qa-             → QA Mumbai
grv_qa2         → QA2 Mumbai
```

### Developer Flow
```
master → feature/vapt-fix → PR to master → merge → cherry-pick to qa-/grv_qa2
```

### Properties Files Scope
- `application.properties` — base config (all envs)
- `application-prod.properties` — prod-specific
- `application-qa_mumbai.properties` — QA env (cherry-picked to qa- branch)
- `application-qa2_mumbai.properties` — QA2 env (cherry-picked to grv_qa2 branch)

---

## Execution Process (Per Repo)

### Step 1: Identify Issues
- Download Wiz report (CSV, Detailed) for the repo
- Identify CVEs (SCA) and secret locations

### Step 2: Apply Fix
- Create feature branch from master
- Add version override in pom.xml (SCA)
- Replace hardcoded secrets with `${ENV_VAR}` placeholders
- Verify: `mvn clean compile -DskipTests`

### Step 3: Create PR
- Push feature branch
- Create PR against master with full description (CVEs, changes, Devtron action required)

### Step 4: Devtron Setup
- Add secret key:value pairs in Devtron Secrets for each environment
- This MUST be done BEFORE deploying the code change

### Step 5: Deploy & Verify
- Merge PR to master
- Deploy via Devtron
- Verify service starts successfully
- Cherry-pick to qa/qa2 branches
- Deploy to qa/qa2 environments

### Step 6: Close Wiz Finding
- After deployment, Wiz rescans and auto-closes if fixed
- Verify in Wiz dashboard that findings show "Resolved"

---

## Progress Tracking

Tracked in: `VAPT_Team_Tracker.xlsx`

| Column | Purpose |
|--------|---------|
| Repo Name | The GitHub repository |
| SCA Status | Pending / Done |
| Secrets Status | Pending / Done |
| Devtron Keys (Prod) | key: value pairs added to Devtron prod |
| Devtron Keys (QA/QA2) | key: value pairs added to Devtron qa/qa2 |
| PR Link | GitHub PR URL per environment |
| Notes | Any issues or special cases |

---

## Completed

| # | Repo | PR | Date |
|---|------|----|------|
| 1 | delhivery/wms-container | [#3522](https://github.com/delhivery/wms-container/pull/3522) | 2 Jun 2026 |
| 2 | delhivery/wms-sdp | [#320 (prod)](https://github.com/delhivery/wms-sdp/pull/320), [#321 (qa)](https://github.com/delhivery/wms-sdp/pull/321), [#322 (qa2)](https://github.com/delhivery/wms-sdp/pull/322) | 2 Jun 2026 |
| 3 | delhivery/wms-pick | [#3258 (prod)](https://github.com/delhivery/wms-pick/pull/3258), [#3260 (qa)](https://github.com/delhivery/wms-pick/pull/3260), [#3259 (qa2)](https://github.com/delhivery/wms-pick/pull/3259) | 3 Jun 2026 |

---

## Risks & Considerations

1. **Devtron secrets must be added BEFORE deploying code** — otherwise the app will fail to start (unresolved placeholders)
2. **Cherry-picks to qa/qa2** — must be done after master merge to keep branches in sync
3. **New Relic uses ERB syntax** (`<%= ENV[""] %>`) not Spring `${}` — different from other properties files
4. **Secrets in git history** — even after removal from code, they remain in git history. The security team has acknowledged this and doesn't require history rewriting.
5. **Tomcat 9.0.x EOL** — March 2027. Long-term plan should be Spring Boot 3.x migration (separate effort).

---

## Contacts

| Role | Name | Email |
|------|------|-------|
| Security team | (from Wiz invite email) | cybersecurity@delhivery.com |
| WMS team lead | Lucky K | lucky.k4@delhivery.com |
| Assigned developer | Kushagra | (you) |

---

## References

- [Wiz Portal](https://app.wiz.io)
- [SDLC Vulnerability Tracker](https://docs.google.com/spreadsheets/d/1Bg_vsZkesNlaUnOATvFbvmhAc4TuJdteRfPcJKoXdbU/edit?gid=1679204386#gid=1679204386)
- [CVE-2023-44487 (HTTP/2 Rapid Reset)](https://github.com/advisories/GHSA-qppj-fm5r-hxr3)
- [CVE-2025-24813 (Tomcat RCE)](https://github.com/advisories/GHSA-83qj-6fr2-vhqg)
- [Apache Tomcat 9.0 Downloads](https://tomcat.apache.org/download-90.cgi)
- [Tomcat 9.0 EOL Notice](https://tomcat.apache.org/tomcat-9.0.x-eos.html)
