# Requirements Specification: SentinelOps (Enterprise Autonomous SecOps Agent)

## 1. Executive Summary
Modern cloud-native engineering pipelines produce an overwhelming volume of security scan outputs (e.g., SAST, SCA, container image scans). Security and engineering teams face severe alert fatigue, leading to delayed remediation of critical vulnerabilities. 

**SentinelOps** is an autonomous, multi-agent AI system built on Microsoft Foundry. It ingests raw vulnerability alerts, evaluates blast radiuses against grounded organizational security baselines, formulates precise dependency and configuration patches, and automates pull request generation while enforcing enterprise approval guardrails.

---

## 2. Functional Requirements (FR)

### FR-1: Multi-Format Alert Ingestion
* The system MUST ingest structured vulnerability payloads (JSON) from standard scanning tools (Semgrep, Trivy, Dependabot, and raw CVE identifiers).
* Alerts MUST be validated against an input schema verifying `source`, `vulnerability_id`, `package_name`, `installed_version`, and `severity_raw`.

### FR-2: Knowledge Base Grounding (RAG)
* The agent MUST evaluate each vulnerability against an enterprise security policy document hosted in Azure AI Search / Microsoft Foundry Knowledge Store.
* The agent MUST classify alerts into compliance tiers:
  * **Critical Violations:** CVSS >= 7.0 or exposed secrets (Mandatory patch SLA < 24h).
  * **Conditional Exceptions:** Dev-only dependencies with no external attack surface.
  * **Major Version Breaking Changes:** Require manual review flag (`HUMAN_REVIEW_REQUIRED`).

### FR-3: External Tool Integration
* **CVE Lookup Tool:** Agent MUST query an external vulnerability catalog to retrieve published CVSS scores, remediation advisories, and fixed versions.
* **Patch Generator Tool:** Agent MUST produce valid, copy-pasteable Git diff patches (e.g., `package.json`, `Dockerfile`, `pom.xml`).
* **Ticketing / PR Dispatch Tool:** Agent MUST format and trigger an automated mock GitHub Pull Request or Jira payload.

### FR-4: Structured Contract Output
* The final agent decision MUST be emitted in strict, un-escaped JSON adhering to the `SentinelRemediationReport` schema.

---

## 3. Non-Functional Requirements (NFR)

* **NFR-1 (Determinism & Guardrails):** The agent must not guess or hallucinate semantic package versions. If a safe upgrade path cannot be confirmed by the tools, it MUST flag the alert for human intervention.
* **NFR-2 (Observability & Tracing):** All agent invocation steps, tool calls, and model latency MUST be logged via Azure Application Insights.
* **NFR-3 (Execution Latency):** Complete triage, policy lookup, and patch formulation pipeline must conclude in under 30 seconds per alert payload.
* **NFR-4 (Security & RBAC):** API keys and Azure credentials must remain stored in environment secrets or Azure Key Vault, never exposed in system prompts.

---

## 4. Evaluation & Success Criteria

1. **Precision:** Zero hallucinations on patch syntax for standard dependency upgrades.
2. **Schema Compliance:** 100% of outputs validate against the target JSON contract.
3. **Enterprise Feasibility:** The agent successfully runs through end-to-end triaging using Microsoft Foundry Agent SDK / REST runtime.
