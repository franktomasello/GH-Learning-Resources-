# GitHub Copilot Premium Request Budgeting Scenarios

This guide outlines two scenarios to help you set up GitHub Copilot in your GitHub Enterprise environment for optimal cost efficiency and usage control.

- Scenario 1: Selective onboarding to Copilot Enterprise for heavy users of Premium Requests.
- Scenario 2: Setting separate spending limits for specific Copilot users.

> Note: Both scenarios require creating a new budget. By default, Copilot Premium Requests have a $0 budget applied, which must be removed for the options below to work properly.

## Scenario 1: Selective Onboarding to Copilot Enterprise for Premium Request Power Users

**Situation:** You want to onboard a subset of users to Copilot Enterprise because it may be more cost-effective for heavy users of Premium Requests.

> Note: Copilot Enterprise is only available for GitHub Enterprise (GHE) customers. It is not available for customers on non-GHE SKUs.

### Background

**Copilot Business:**

- 300 Premium Requests/month
- $19/user/month
- Additional requests: $0.04 per request

**Copilot Enterprise:**

- 1,000 Premium Requests/month
- $39/user/month
- Additional requests: $0.04 per request

### Cost Comparison

| Monthly Premium Requests | Copilot Business (CB) Total Cost | Copilot Enterprise (CE) Total Cost | Savings (CE vs CB) |
| --- | --- | --- | --- |
| 300 | $19 (License) + $0 | $39 (License) + $0 | -$20 |
| 500 | $19 + $8 (200 x $0.04) = $27 | $39 + $0 = $39 | -$12 |
| 800 | $19 + $20 (500 x $0.04) = $39 | $39 + $0 = $39 | $0 (Breakeven) |
| 1,000 | $19 + $28 (700 x $0.04) = $47 | $39 + $0 = $39 | $8 |
| 2,000 | $19 + $68 (1,700 x $0.04) = $87 | $39 + $40 (1,000 x $0.04) = $79 | $8 |

**Tip:** Any user making 800 or more Premium Requests per month will save money by being on Copilot Enterprise.

### Directions

1. Download your GitHub Copilot Premium Request Usage Report following these instructions.
2. Aggregate the report to identify users who exceed 800 Premium Requests/month.
3. Create a new organization within your GitHub Enterprise.
4. Invite the power users to the new organization.
5. Assign Copilot Enterprise licenses to those users in the new organization.
6. Continue monitoring usage to ensure Copilot Enterprise remains the most cost-effective option for these users.

## Scenario 2: Setting Separate Spending Limits for Specific Copilot Users

**Situation:** You want to set a separate budget for certain users to ensure they can continue using Premium Requests beyond their default entitlement.

### Background

There are currently two supported methods to assign custom spending limits for specific users:

- Create a separate Organization and set a budget specific to that Organization.
- Create a Cost Center, assign users to it, and set a budget specific to that Cost Center.

> Note: A user can belong to only one Cost Center at a time. If you already use Cost Centers for other purposes, only Option 1 may be viable.

> Note: Copilot Premium Requests have a default $0 budget at the Enterprise level. To manage spending, you’ll need to delete this default budget and create two separate budgets: one for the users you want to allocate additional Copilot spend to, and another for the users you do not want to allocate additional spend to (this budget can remain at $0). Each budget should be scoped to the appropriate organization or Cost Center that contains the relevant users.

### Option 1: Use a Separate Organization Budget

1. Download your GitHub Copilot Premium Request Usage Report following these instructions.
2. Aggregate the report to identify users who may benefit from an increased budget.
3. Delete the default $0 Copilot Premium Request Budget.
4. Create a new organization within your GitHub Enterprise.
5. Invite the selected users to the new organization.
6. Assign Copilot licenses to those users.
7. Create a budget for that organization at the Enterprise level for users who should have additional premium requests.
8. Configure the budget:
	- Budget Type: SKU-level budget
	- Product: Copilot
	- SKU: Copilot Premium Request
	- Budget Scope: The newly created organization
	- Budget Amount: Your desired limit
	- Alerts: Optional thresholds for budget notifications
9. Create a budget for that organization at the Enterprise level for users who should have NO additional premium requests.
10. Configure the budget:
	- Budget Type: SKU-level budget
	- Product: Copilot
	- SKU: Copilot Premium Request
	- Budget Scope: The original organization
	- Budget Amount: Your desired limit
	- Alerts: Optional thresholds for budget notifications
11. Monitor usage to ensure the budget aligns with actual usage patterns.

### Option 2: Use a Cost Center Budget

1. Download your GitHub Copilot Premium Request Usage Report following these instructions.
2. Aggregate the report to identify users who may benefit from an increased budget.
3. Create a new Cost Center.
4. Assign users who should have additional premium requests to the Cost Center using the API.
5. Create a budget for the Cost Center at the Enterprise level.
6. Configure the budget:
	- Budget Type: SKU-level budget
	- Product: Copilot
	- SKU: Copilot Premium Request
	- Budget Scope: Cost Center for additional premium requests
	- Budget Amount: Your desired limit
	- Alerts: Optional thresholds for budget notifications
7. Create a new Cost Center.
8. Assign users who should have additional NO premium requests to the Cost Center using the API.
9. Create a budget at the Enterprise level.
10. Configure the budget:
	- Budget Type: SKU-level budget
	- Product: Copilot
	- SKU: Copilot Premium Request
	- Budget Scope: Cost Center for NO premium requests
	- Budget Amount: Your desired limit
	- Alerts: Optional thresholds for budget notifications
11. Continue monitoring Premium Request usage to validate the budget's effectiveness.

**Tip:** You can combine both scenarios if you'd like specific Copilot Enterprise users to have different spending limits than other Copilot Enterprise users.