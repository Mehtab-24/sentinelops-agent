### `BUILD.MD`

# Build & Implementation Guide: SentinelOps

Follow this exact roadmap to configure the agent inside Microsoft Foundry and deploy the local evaluation driver.

---

## Step 1: Environment & Cloud Setup

1. **Login to Microsoft Foundry:**
   Navigate to [Azure AI Foundry Portal](https://ai.azure.com/nextgen) using your Azure account.
2. **Select Workspace:**
   * **Project:** `secops-sentinel-hub`
   * **Region:** `southeastasia` (or assigned allowed region)
3. **Deploy Model:**
   * Navigate to **Models + Endpoints** -> **Deploy Model**.
   * Deploy `gpt-4o` (or `gpt-4o-mini`) with standard quota. Name deployment: `sentinel-gpt4o`.

---

## Step 2: Upload Knowledge Base (RAG)

1. Create a local file named `enterprise_sec_policy.md` with the following content:
   ```markdown
   # Enterprise Security Policy (v2026.3)
   - Any dependency with CVSS >= 7.0 MUST be triaged and have a remediation branch created within 24 hours.
   - Minor and patch version upgrades can be auto-approved if unit test coverage passes.
   - Any major version bump (breaking API changes) requires `human_review_required: true`.
   - Production Docker images running as root are non-compliant and require an updated non-root user directive.
