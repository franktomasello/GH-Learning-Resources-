# GitHub Copilot Premium Requests — Overage Budgets & Cost Monitoring Runbook (GHEC Enterprise)

Below are the same three runbooks (enterprise-wide, by org, by cost center) with an added "why you'd choose each scope" section.

> **Scope note:** This runbook is for GitHub Enterprise Cloud (enterprise billing + Copilot Business/Enterprise).

---

## 🚨 Critical Warning (Read This First)

**Creating new budgets without deleting or editing existing budgets does not override them.** If any applicable budget with "Stop usage when budget limit is reached" enabled is exhausted, additional premium requests are blocked.

---

## Why You'd Budget at Each Level

- **Enterprise-wide budget (one global cap):** Best when you want a single guardrail on total premium-request overage spend across the whole company—simple for Finance and early rollout. **Tradeoff:** one heavy-consuming org can burn the shared cap and block everyone if "Stop usage…" is enabled.

- **Org-level budget (cap per org):** Best when orgs map to different teams/product lines, you're doing phased rollout, or you want to prevent one org from impacting others.

- **Cost-center budget (best for chargeback):** Best when you want budgets aligned to financial entities (BU/department/project), including when costs span multiple orgs/repos/users. Cost centers support allocation + reporting + applying budgets to the grouping.

---

## 0) One-Time Prerequisite for All Three Approaches: Allow (or Block) Overages via Policy

The "Premium request paid usage" policy is the gate for whether users can go past their included allowance (and incur overage charges).

> **Quick sanity check (Dec 2025):** Accounts created before Aug 22, 2025 may have had a default $0 Copilot premium request budget; beginning Dec 2, 2025, GitHub started removing those account-level $0 budgets for Enterprise/Team. Still: check Budgets & alerts and delete/edit any $0 budget if present.

### Step 0.1 — Open Copilot Policy Controls (Enterprise)

**Click path (UI):**
1. Top-right profile picture → **Enterprise** (or **Enterprises**) → select your enterprise
2. At the top of the page, click **AI controls**
3. In the sidebar, click **Copilot**

### Step 0.2 — Set Premium Request Paid Usage

On the policy page:

- Set **Premium request paid usage** to:
  - **Enabled** (allow overages, subject to budgets), or
  - **Disabled** (block overages entirely)

If your goal is "allow overages but cap spend," use **Enabled** + a budget with **Stop usage when budget limit is reached**.

---

## Guide A — Enterprise-Wide Overage Budget (Single Cap for the Whole Enterprise)

### A1) Navigate to Budgets and Alerts (Enterprise)

**Click path (UI):**
1. Profile picture → **Enterprise / Enterprises** → select your enterprise
2. Click **Billing and licensing** (wording may appear as "Billing & licensing")
3. Click **Budgets and alerts**

### A2) Create the Enterprise-Wide Premium Request Budget

**Click path (UI):**
1. **Budgets and alerts** → **New budget**
2. Under **Budget type**, pick one:
   - **Bundled premium requests budget** (recommended for most), or
   - **Individual/SKU-level budgets** (separate budgets per AI tool / SKU)
3. Under **Budget scope**, choose **Whole enterprise**
4. Set the **Budget** ($ amount)
5. Enable **Stop usage when budget limit is reached** (hard cap for metered premium requests)
6. Configure alerts (e.g., 75/90/100%) + recipients
7. Click **Create budget**

> ✅ **Re-emphasis:** A new enterprise-wide budget does not cancel older applicable budgets. If any applicable "Stop usage…" budget hits its limit, premium requests can be blocked even if this new budget still has room.

### A3) Monitor Costs (Enterprise-Wide)

#### A3.1 — Premium Request Analytics

**Click path (UI):**
1. **Enterprise** → **Billing and licensing**
2. **Usage** → **Premium request analytics**

#### A3.2 — Download a Cost/Usage Report (CSV)

**Click path (UI):**
1. From **Metered Usage** or **Premium request analytics**, click **Get usage report**
2. Specify report details
3. Click **Email me the report**

---

## Guide B — Budget "By Org" (Cap Premium Request Spend per Organization)

### B1) Navigate to Budgets and Alerts

Same entry as Guide A:
- **Enterprise** → **Billing and licensing** → **Budgets and alerts**

### B2) Create an Org-Scoped Premium Request Budget

**Click path (UI):**
1. **Budgets and alerts** → **New budget**
2. **Budget type:** Bundled premium requests or Individual/SKU-level
3. **Budget scope:** choose **Organization** (then select the org)
4. Set **Budget** ($), enable **Stop usage when budget limit is reached**, set alerts → **Create budget**

> ✅ **Re-emphasis:** Creating a new org budget does not override an existing enterprise/org/cost-center budget. If any applicable "Stop usage…" budget is exhausted, additional premium requests are blocked.

### B3) Monitor Costs "By Org"

1. **Enterprise** → **Billing and licensing**
2. **Usage** → **Premium request analytics**
3. Use filters/grouping for org-level breakdowns

---

## Guide C — Budget "By Cost Center" (Best for Chargeback + BU Ownership)

Cost centers let you attribute spend to BUs and apply budgets to that grouping.

### C1) Create a Cost Center (If You Don't Already Have One)

**Click path (UI):**
1. Profile picture → **Enterprise / Enterprises** → select your enterprise
2. Click **Billing and licensing**
3. Click **Cost centers**
4. Click **New cost center**
5. Enter **Name**
6. Under **Resources**, add organizations, repositories, and/or users
   - **Note:** A resource (org/repo/user) can only be assigned to one cost center at a time; adding it elsewhere moves it.
7. Click **Create cost center**

### C2) Create a Premium Request Budget Scoped to That Cost Center

**Click path (UI):**
1. **Enterprise** → **Billing and licensing** → **Budgets and alerts**
2. **New budget**
3. **Budget type:** Bundled premium requests or Individual/SKU-level
4. **Budget scope:** choose **Cost center** (select the cost center)
5. Set **Budget** ($), enable **Stop usage when budget limit is reached**, set alerts → **Create budget**

> ✅ **Re-emphasis:** Adding a cost-center budget does not override existing enterprise/org budgets. If any applicable "Stop usage…" budget is exhausted, premium requests can be blocked even if other budgets still have remaining spend.

### C3) Monitor Costs "By Cost Center"

1. **Enterprise** → **Billing and licensing**
2. **Usage** → **Premium request analytics** (group/filter by cost center)
3. Optional export: **Get usage report** → **Email me the report**

---

**Updated:** December 2025