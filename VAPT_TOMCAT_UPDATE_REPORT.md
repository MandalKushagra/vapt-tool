# VAPT Tomcat Embed Core 9.0.118 Update Report
**Date:** 2026-06-24
**Change:** Replace `<tomcat.version>9.0.100</tomcat.version>` property with explicit `tomcat-embed-core 9.0.118` dependency

## Summary
- **PR #3550 (wms-container duplicate):** ✅ Closed
- **Prod branches updated:** 20/23 repos
- **QA/Staging branches updated:** 5 repos
- **Skipped (no branch found):** Listed below

---

## Prod Branch (fix/vapt-remediation-prod) Results

| # | Repo | Status | Commit |
|---|------|--------|--------|
| 1 | wms-container | ✅ Updated | c869cfb |
| 2 | wms-pick | ✅ Updated | 669c671 |
| 3 | telescope-order-amplifier | ✅ Updated | 9bf58be |
| 4 | telescope-communications | ✅ Updated | 67f57b3 |
| 5 | telescope_version2 | ✅ Updated | 86e5a31 |
| 6 | telescope_db_writer | ✅ Updated | d921bd1 |
| 7 | telescope-order-version2 | ✅ Updated | ef14931 |
| 8 | e2e-transport-selection | ✅ Updated | ffbb2f1 |
| 9 | wms-notification-retry-service | ✅ Updated | 0f95784 |
| 10 | wms-platform-integrator | ✅ Updated | 94680b6 |
| 11 | wms-order-request | ✅ Updated | 6cb668a |
| 12 | e2e-order-management | ✅ Updated | 9731baf |
| 13 | telescope-order-db-writer | ✅ Updated | 3acdaaf |
| 14 | wms-gim | ✅ Updated | cb83d7f |
| 15 | wms-pack | ✅ Updated | 80aeed9 |
| 16 | telescope-permission-version2 | ✅ Updated | 21891b7 |
| 17 | wms-data-dev | ✅ Updated | 4e8cc43 |
| 18 | telescope_amplifier | ✅ Updated | a3bcd6f |
| 19 | wms-amplifier | ✅ Updated | 393f541 |
| 20 | wms-request-fetcher | ✅ Updated | 28819f2 |
| 21 | wms-workflow-rule-engine | ✅ Updated | c4f2fd2 |
| 22 | telescope-data-sync | ✅ Updated | 04b5ff0 |
| 23 | wms-sdp | ❌ Branch not found | - |
| 24 | phuze-client-service | ❌ Branch not found | - |

---

## QA/Staging Branch Results

| # | Repo | Branch | Status | Commit |
|---|------|--------|--------|--------|
| 1 | wms-pick | fix/vapt-remediation-qa | ✅ Updated | b79a757 |
| 2 | telescope-order-amplifier | fix/vapt-remediation-qa | ✅ Updated | d36e1e1 |
| 3 | telescope-communications | fix/vapt-remediation-qa | ✅ Updated | b84d0ec |
| 4 | wms-gim | fix/vapt-remediation-stg | ✅ Updated | 598e2dd |
| 5 | wms-pack | fix/vapt-remediation-qa | ✅ Updated | f378cdc |
| 6 | telescope_version2 | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 7 | telescope_db_writer | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 8 | telescope-order-version2 | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 9 | wms-platform-integrator | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 10 | wms-order-request | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 11 | telescope-order-db-writer | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 12 | phuze-client-service | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 13 | telescope-permission-version2 | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 14 | telescope_amplifier | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 15 | wms-amplifier | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 16 | wms-request-fetcher | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 17 | wms-workflow-rule-engine | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 18 | telescope-data-sync | fix/vapt-remediation-qa | ❌ Branch not found | - |
| 19 | wms-sdp | fix/vapt-remediation-qa | ❌ Branch not found | - |

---

## Skipped Repos (as instructed)
- **gm-amz-mlts-service** — Spring Boot 1.5.4, incompatible with Tomcat 9.x
- **wms-user-management** — Python, no tomcat
- **wms-eye** — Already done (PR #110)
- **wms-container QA branch** — Already done (PR #3526)

---

## Notes
- QA branches that don't exist yet were likely not created prior to this update. They may need to be created from their respective main/master branches and then updated.
- wms-sdp and phuze-client-service don't have the fix/vapt-remediation-prod branch at all — may need to be created fresh.
