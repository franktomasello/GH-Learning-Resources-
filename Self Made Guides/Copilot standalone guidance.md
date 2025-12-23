# Copilot standalone guidance 

<br>

## Most Optimal Way: Enterprise Teams Assignment

For **1,000 users**, Enterprise Teams is definitively the best approach. Here's why from the transcript:

- **80%+ of adopters use Enterprise Teams** for bulk assignment
- **API support** available for automation
- **IDP integration** possible for sync with identity providers
- Easier ongoing management than 1k individual assignments

* * *

## Step-by-Step Implementation:

### 1\. **Navigate to Enterprise Settings**

- Go to your SIE EMU enterprise account
- Access the **Licensing page** (not org-level settings)

### 2\. **Create Enterprise Team for Perforce Users**

- Navigate to **Enterprise Teams**
- Click "Create Team"
- Name it something like "Perforce-Copilot-Users" or "PlayStation-Studios-Copilot"

### 3\. **Add Users to the Team**

**Option A - Manual (smaller batches):**

- Add users directly through the UI

**Option B - API (recommended for 1k users):**

- Use the Enterprise Teams API to bulk add users
- Transcript confirms: _"API support for all this new thing we're launching"_ (09:45)

**Option C - IDP Sync (best long-term):**

- Configure the Enterprise Team to sync with an IDP user group
- Any changes in IDP automatically update Copilot access
- Transcript: _"you can configure a team to link with an IDP user group"_ (06:33)

### 4\. **Assign Copilot to the Enterprise Team**

- Select your newly created team
- Click "Assign Copilot"
- This grants Copilot access to all team members

### 5\. **Verify Assignment**

> Note: There's a **cooldown period** - seats show up within a few minutes

- Users get **access immediately** even if dashboard takes time to update
- Go to Licensing dashboard to verify seat count

### 6\. **Set Copilot Policies (if needed)**

- Configure any enterprise-level Copilot policies
- For unaffiliated users, the "Policy for Enterprise Assigned Users" applies
- Default is disabled, so enable features you want them to access

* * *

## Important Notes:

✅ **Users remain unaffiliated** - they never enter an organization, so **zero GHE licenses consumed**

✅ **One license per user** - even if assigned multiple times, they only consume one Copilot seat (deduplication happens automatically)

✅ **IDP integration recommended** - set it up once, forget about manual management

⚠️ **Cooldown period** - licensing dashboard may take a few minutes to reflect changes (users still get immediate access)

⚠️ **Enterprise team has a 500 users per team limit _,_ so they might need to spilt out into multiple teams**  

**⚠️  Ideally, we (product) want teams to align with their actual function like `platform-engineers` `ai-engineers` , not `copilot-1` `copilot-2`** 

* * *

## Timeline:

This can be set up **today** - it's GA and available now for all EMU accounts.

<br>