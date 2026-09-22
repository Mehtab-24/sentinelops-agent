# Enterprise Security SLA & Policy Standard (v2026.3)

## 1. Vulnerability Classification & SLAs
- **CRITICAL / HIGH Severity (CVSS >= 7.0):** Requires mandatory remediation branch and pull request generation within a 24-hour SLA window.
- **MEDIUM Severity (CVSS 4.0 - 6.9):** Remediation required within 7 business days.
- **LOW Severity (CVSS < 4.0):** Triaged during regular sprint maintenance cycles.

## 2. Dependency Upgrade Guardrails
- **Automated PR Dispatch:** Permitted for minor and patch SemVer increments (e.g., `1.4.0` -> `1.5.2`) provided transitive dependencies clear CVE criteria.
- **Breaking Changes:** Any major SemVer version bump (e.g., `v3.x` to `v4.x` or breaking runtime signatures) CANNOT be merged automatically. The agent must emit:
  `"human_review_required": true`
  and set the action to `FLAGGED_FOR_MANUAL_REVIEW`.

## 3. Container & Infrastructure Rules
- Base images must not execute as `root`. Non-root `USER` directives must be enforced.
- Exposed secrets or credentials in repository files require immediate pipeline suspension.