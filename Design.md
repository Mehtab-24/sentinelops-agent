# System Architecture & Design Specification: SentinelOps

## 1. System Architecture

SentinelOps utilizes an **Orchestrator-Worker Multi-Agent Pattern** hosted in Microsoft Foundry, backed by Azure AI Search and Azure Application Insights.

   [CI/CD / Ingest Webhook]
              │
              ▼ Raw Alert Payload (JSON)
   ┌───────────────────────────────┐
   │   SentinelOps Orchestrator    │
   │   (Azure OpenAI GPT-4o)       │
   └──────────────┬────────────────┘
                  │
 ┌────────────────┴────────────────┐
 ▼                                 ▼
┌────────────────────────────┐    ┌────────────────────────────┐
│   Enterprise Policy RAG    │    │      CVE Lookup Tool       │
│  (Azure AI Search Index)   │    │  (External / Mock API)     │
└─────────────┬──────────────┘    └────────────┬───────────────┘
│                                │
└────────────────┬───────────────┘
│ Context Aggregation
▼
┌───────────────────────────────────────────────┐
│             Remediation Engine                │
│  - SemVer Bump / Git Diff Generation          │
│  - Breaking Change & Blast Radius Analysis    │
└───────────────────────┬───────────────────────┘
│
▼
┌───────────────────────────────────────────────┐
│         PR & Dispatch Integrator              │
│  - Strict JSON Output Generation              │
│  - GitHub Mock PR / Webhook Trigger           │
└───────────────────────┬───────────────────────┘
│
▼
[Application Insights Telemetry & Audit Logs]


---

## 2. Component Specifications

### 2.1 Orchestrator Agent
* **Role:** Acts as the primary router and deterministic supervisor.
* **Core Responsibilities:**
  1. Validates incoming alert integrity.
  2. Queries Enterprise Security Baseline via Vector Search.
  3. Triggers the CVE lookup tool.
  4. Delegates patch creation to the patch engine.
  5. Validates output conformity before dispatch.

### 2.2 Grounding Vector Store (Enterprise Policy)
* **Storage:** Azure AI Search connected via Microsoft Foundry Vector Index.
* **Document Schema:** Markdown/PDF containing corporate SLAs, vulnerability classification tiers, and dependency update permissions.

### 2.3 Tools & Function Calling Interfaces
The agent leverages tool-calling interfaces with defined JSON schemas:

#### Tool 1: `fetch_cve_advisory`
```json
{
  "name": "fetch_cve_advisory",
  "description": "Retrieves published severity, fixed versions, and advisory notes for a CVE identifier.",
  "parameters": {
    "type": "object",
    "properties": {
      "cve_id": { "type": "string", "description": "The CVE identifier (e.g., CVE-2023-45133)" },
      "package_name": { "type": "string", "description": "Affected package or dependency name" }
    },
    "required": ["cve_id", "package_name"]
  }
}
