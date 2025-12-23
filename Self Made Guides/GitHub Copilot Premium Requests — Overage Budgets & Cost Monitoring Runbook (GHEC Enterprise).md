# **GitHub Copilot Premium Requests — Overage Budgets & Cost Monitoring Runbook (GHEC Enterprise)**

**<br>
**

**Below are the same** **three runbooks** **(enterprise-wide, by org, by cost center), with an added** **“why you’d choose each scope”** **section—everything else stays as-is.**

<br>

* * *

<br>

<br>

## **🚨 Critical warning (read this first)**

<br>

<br>

**Creating new budgets without deleting an existing budget does not override the existing budget. If any applicable budget with “Stop usage when budget limit is reached” enabled is exhausted, additional premium requests are blocked.** 

<br>

* * *

<br>

<br>

## **Why you’d budget at each level**

<br>

<br>

- **Enterprise-wide budget (one global cap):** **Best when you want a** **single “guardrail”** **on total premium-request overage spend across the whole company—simple to operate, easy for Finance, and good for early rollout. Tradeoff:** **one heavy-consuming org can burn the shared cap and block everyone** **if “Stop usage…” is enabled.** 
- **Org-level budget (cap per org):** **Best when orgs represent** **different product lines/teams****, you’re doing a** **phased Copilot rollout****, or you want to prevent one org from impacting others. Org budgets also make it easier to align ownership to** **org admins** **and keep spend predictable by org.** 
- **Cost-center budget (best for chargeback):** **Best when your company wants budgets aligned to** **financial entities (BU/department/project)****—especially when costs span multiple orgs/repos/users. Cost centers are designed for** **allocation + reporting + control at scale****, and you can group** **users, orgs, and repos** **under a single cost center for tracking/budgeting.** 

<br>

<br>

* * *

<br>

<br>

# **0) One-time prerequisite for all three approaches: allow (or block) overages via policy**

<br>

<br>

**This policy is the “gate” for whether users can go past their included premium request allowance.** 

<br>

<br>

### **Step 0.1 — Open Copilot policy controls (Enterprise)**

<br>

<br>

**Click path (UI):**

<br>

1. **Top-right profile picture** **→** **Enterprise** **(or** **Enterprises****) → select your** **enterprise** 
2. **At the top of the page, click** **AI controls** 
3. **In the sidebar, click** **Copilot** 

<br>

<br>

<br>

### **Step 0.2 — Set Premium request paid usage**

<br>

<br>

**On the policy page:**

<br>

- **Set** **Premium request paid usage** **to:**
    - **Enabled** **(allow overages, subject to budgets), or**
    - **Disabled** **(block overages entirely)** 

<br>

<br>

> **If your goal is “allow overages but cap spend,” you typically want** **Enabled** **+ a budget with** **Stop usage when budget limit is reached****.** 

<br>

* * *

<br>

<br>

# **Guide A — Enterprise-wide overage budget (single cap for the whole enterprise)**

<br>

<br>

<br>

## **A1) Navigate to Budgets and alerts (Enterprise)**

<br>

<br>

**Click path (UI):**

<br>

1. **Top-right profile picture** **→** **Enterprise / Enterprises** **→ select your** **enterprise** 
2. **Navigate to** **Billing & Licensing** 
3. **Click** **Budgets and alerts** 

<br>

<br>

<br>

## **A2) Create the enterprise-wide premium request budget**

<br>

<br>

**Click path (UI):**

<br>

1. **Budgets and alerts** **→** **New budget** 
2. **Under** **Budget Type****, pick one:**
    - **Bundled premium requests budget** **(recommended for most)** 
    - **SKU-level budget** **(more granular per AI product/SKU)** 

3. **Under** **Budget scope****, choose** **Whole enterprise** 
4. **Set the** **Budget** **($ amount)** 
5. **Enable** **Stop usage when budget limit is reached** **(hard cap)** 
6. **Configure** **Alerts** **(75/90/100%) and recipients** 
7. **Click** **Create budget** 

<br>

<br>

**✅** **Re-emphasis (don’t skip):** **Adding a new enterprise-wide budget** **does not cancel** **older applicable budgets. If** **any** **applicable budget with** **Stop usage…** **hits its limit,** **premium requests can be blocked****, even if your new budget still has room.** 

<br>

<br>

## **A3) Monitor costs (enterprise-wide)**

<br>

<br>

<br>

### **A3.1 — Premium request analytics**

<br>

<br>

**Click path (UI):**

<br>

1. **Enterprise →** **Billing & Licensing**
2. **Go to** **Usage** **→** **Premium request analytics** 

<br>

<br>

<br>

### **A3.2 — Download a cost/usage report (CSV)**

<br>

<br>

**Click path (UI):**

<br>

1. **From** **Metered Usage** **or** **Premium request analytics****, click** **Get usage report** 
2. **Specify report details**
3. **Click** **Email me the report** 

<br>

<br>

* * *

<br>

<br>

# **Guide B — Budget “by org” (cap premium request spend per organization)**

<br>

<br>

<br>

## **B1) Navigate to Budgets and alerts**

<br>

<br>

**Same entry as Guide A:**

<br>

- **Enterprise →** **Billing & Licensing** **→** **Budgets and alerts** 

<br>

<br>

<br>

## **B2) Create an org-scoped premium request budget**

<br>

<br>

**Click path (UI):**

<br>

1. **Budgets and alerts** **→** **New budget** 
2. **Budget Type:** **choose:**
    - **Bundled premium requests budget****, or**
    - **SKU-level budget** 

3. **Budget scope:** **choose** **Organization** **(then select the org)** 
4. **Set** **Budget** **($), enable** **Stop usage when budget limit is reached****, set alerts, then** **Create budget** 

<br>

<br>

**✅** **Re-emphasis (your exact scenario):** **Creating a new org budget** **does not override** **an existing org/enterprise/cost-center budget. If** **any applicable** **budget with** **Stop usage…** **enabled is exhausted,** **additional premium requests are blocked****.** 

<br>

<br>

## **B3) Monitor costs “by org”**

<br>

<br>

**Click path (UI):**

<br>

1. **Enterprise →** **Billing & Licensing**
2. **Usage** **→** **Premium request analytics** 

**Then use the page’s filters/grouping to view org-level breakdowns.** 

<br>

<br>

* * *

<br>

<br>

# **Guide C — Budget “by cost center” (best for chargeback + BU ownership)**

<br>

<br>

**Cost centers let you attribute usage/spend to business units and apply budgets to them.** 

<br>

<br>

## **C1) Create a cost center (if you don’t already have one)**

<br>

<br>

**Click path (UI):**

<br>

1. **Top-right profile picture** **→** **Enterprise / Enterprises** **→ select your enterprise** 
2. **Go to** **Billing and licensing** 
3. **Click** **Cost centers** 
4. **Click** **New cost center** 
5. **Enter** **Name**
6. **Add** **organizations, repositories, and/or users** **to the cost center** 
7. **Click** **Create cost center** 

<br>

<br>

<br>

## **C2) Create a premium request budget scoped to that cost center**

<br>

<br>

**Click path (UI):**

<br>

1. **Enterprise →** **Billing & Licensing** **→** **Budgets and alerts** 
2. **New budget** 
3. **Budget Type:**
    - **Bundled premium requests budget****, or**
    - **SKU-level budget** 

4. **Budget scope:** **choose** **Cost center** **(then select your cost center)** 
5. **Set** **Budget** **($), enable** **Stop usage when budget limit is reached****, set alerts,** **Create budget** 

<br>

<br>

**✅** **Re-emphasis (most common “why did it block?” root cause):** **Adding a cost-center budget** **does not override** **existing enterprise/org budgets. If** **any** **applicable budget with** **Stop usage…** **is exhausted, premium requests can be blocked even if other budgets still have remaining spend.** 

<br>

<br>

## **C3) Monitor costs “by cost center”**

<br>

<br>

**Click path (UI):**

<br>

1. **Enterprise →** **Billing & Licensing**
2. **Usage** **→** **Premium request analytics** **(filter/group by cost center)** 
3. **Optionally export via** **Get usage report** **→** **Email me the report** 

<br>

<br>