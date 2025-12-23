## Guide to setup EMU + EntraID (OIDC) + Attach Azure Sub + Turn on Copilot

# 📘 GHEC EMU Enterprise Setup Runbook (Revised & Verified)

**Last Updated:** December 2025  
**Scope:**

- **Identity:** Microsoft Entra ID (OIDC) for SSO
- **Provisioning:** SCIM (Automatic User Provisioning)
- **Billing:** Azure Subscription (Metered)
- **Copilot:** Business/Enterprise (Enable & Assign via Teams or Enterprise Teams)

* * *

## 0) Prerequisites (Critical)

### People & Permissions

| Role | Required Permissions |
| --- | --- |
| **GitHub** | Access to the setup user (`@SHORT-CODE_admin`) email to set the initial password |
| **Microsoft Entra ID** | A **Global Administrator** to consent to the "GitHub Enterprise Managed User (OIDC)" app during SSO setup |
| **Azure Billing** | A user who can provide **tenant-wide admin consent** (Global Admin, Application Admin, or Cloud Application Admin) AND has **Owner** rights on the target Azure Subscription |

> ⚠️ **Important:** Being an Entra Global Admin alone is **not sufficient** for the billing step. You must also have Owner permissions on the specific Azure Subscription resource to select it during connection.

### Hosting Location

Confirm if the enterprise is on:

- **GitHub.com** — Standard cloud hosting
- **GHE.com** — Data Residency (dedicated subdomain)

This affects your SCIM Tenant URL format in Step 4.

### Before You Begin

Ensure you have:

- \[ \] Setup user invitation email received
- \[ \] Access to Microsoft Entra admin center
- \[ \] Azure subscription ID ready
- \[ \] Secure password manager for storing recovery codes and tokens

* * *

## 1) Create/Secure the EMU Setup User

_The setup user (`@SHORT-CODE_admin`) is the only local account that can bypass SSO in emergencies._

1. Open the "setup user invite" email in a **private/incognito browser window**.
2. Set a strong password (store in secure vault).
3. **Immediately enable 2FA:**
    - Navigate to: Profile picture → **Settings** → **Password and authentication**
    - Configure a TOTP app (recommended) or other 2FA method
    - Download and securely store your **personal 2FA recovery codes**

> ⚠️ **Critical (January 2025 Change):** All subsequent logins for the setup user require either a successful 2FA challenge OR use of an enterprise recovery code. If you do not save your enterprise recovery codes (generated in Step 3), you will be locked out.

4. Store credentials in a secure company vault (e.g., 1Password, LastPass, Azure Key Vault).

> 📝 **Note:** If you ever need to reset the setup user password, you must contact [GitHub Support](https://support.github.com/). The standard email-based password reset does not work for setup users.

* * *

## 2) Create the SCIM Token

_This token allows Microsoft Entra ID to provision users to GitHub via SCIM._

### Navigation Path (GitHub)

1. Sign in as the setup user (`@SHORT-CODE_admin`)
2. Profile picture (top-right) → **Settings**
3. Left sidebar: **Developer settings**
4. **Personal access tokens** → **Tokens (classic)**
5. **Generate new token** → **Generate new token (classic)**

### Token Configuration

| Setting | Value |
| --- | --- |
| **Note** | `SCIM Token for Entra ID` (or similar descriptive name) |
| **Expiration** | **No expiration** ⚠️ If this expires, provisioning stops entirely |
| **Scopes** | Select **only** `scim:enterprise` |

6. Click **Generate token**
7. **Copy the token immediately** — you cannot view it again

> 📝 **Store this token securely.** You will need it when configuring provisioning in Microsoft Entra ID (Step 4).

* * *

## 3) Enable Microsoft Entra ID OIDC SSO

_Connects identity so users can authenticate via your IdP._

### Navigation Path (GitHub)

1. Sign in as `@SHORT-CODE_admin`
2. Profile picture → **Your enterprises** → Select your enterprise
3. At the top of the page, click the **Identity provider** tab
4. Under "Identity Provider", click **Single sign-on configuration**
5. Under "OIDC single sign-on", select **Enable OIDC configuration**
6. Click **Save** — you will be redirected to Microsoft

### In Microsoft Entra ID (during redirect)

7. Sign in as a user with **Global Administrator** rights
8. Review the permissions requested for "GitHub Enterprise Managed Users with OIDC"
9. ✅ Check **"Consent on behalf of your organization"**
10. Click **Accept**

### Back in GitHub

11. **Save your Enterprise Recovery Codes:**
    - Click **Download**, **Print**, or **Copy**
    - Store these securely — they are separate from your personal 2FA codes
    - These codes allow emergency access if your IdP becomes unavailable
12. Click **Enable OIDC Authentication**

> ✅ **Verification:** After completing this step, the setup user can still access the enterprise using their local credentials, but all other users will authenticate via Entra ID.

* * *

## 4) Configure SCIM Provisioning

_Pushes users and groups from Microsoft Entra ID to GitHub automatically._

### 4A) Determine Your Tenant URL

| Hosting | Tenant URL Format |
| --- | --- |
| **GitHub.com** | `https://api.github.com/scim/v2/enterprises/{enterprise-slug}` |
| **GHE.com (Data Residency)** | `https://api.{subdomain}.ghe.com/scim/v2/enterprises/{subdomain}` |

> 💡 **Example:** If your enterprise URL is `https://github.com/enterprises/acme-corp`, your Tenant URL is `https://api.github.com/scim/v2/enterprises/acme-corp`

### 4B) Configure Microsoft Entra ID

1. Go to [entra.microsoft.com](https://entra.microsoft.com/) → **Enterprise applications**
2. Select **GitHub Enterprise Managed User (OIDC)** (created automatically during Step 3)
    - If not present, add it from the application gallery
3. Click the **Provisioning** tab → **Get started**
4. Set **Provisioning Mode** to **Automatic**
5. Under **Admin Credentials**:

| Field | Value |
| --- | --- |
| **Tenant URL** | Your URL from Step 4A |
| **Secret Token** | The PAT created in Step 2 |
6. Click **Test Connection** — must show success ✅
7. Click **Save**

### 4C) Configure Provisioning Settings

8. Scroll to **Settings** section:
    - **Scope:** Select "Sync only assigned users and groups"
    - **Provisioning Status:** Toggle to **On**
9. Click **Save**

### 4D) Assign Users and Groups

10. Go to **Users and groups** tab in the Entra application
11. Click **Add user/group**
12. Add a test user or pilot group (e.g., "GitHub-Admins" or "GitHub-Pilot-Users")
13. For users who need Enterprise Owner role, set the **Role** attribute accordingly

### 4E) Initial Sync

- **Automatic:** Wait ~40 minutes for the initial sync cycle
- **Manual testing:** Use **Provision on demand** to test individual users immediately

> ⚠️ **Important:** The combination of Okta and Entra ID for SSO and SCIM (in either order) is explicitly **not supported**. GitHub's SCIM API will return errors if this combination is configured.

* * *

## 5) Create Organization(s) Inside the Enterprise

_EMU enterprises cannot invite or import existing organizations — you must create them fresh._

### Navigation Path (GitHub)

1. Profile picture → **Your enterprises** → Select your enterprise
2. **Organizations** tab → **New organization**
3. Enter organization name (e.g., `acme-engineering`, `acme-platform`)
4. Click **Create organization**

> 💡 **Best Practice:** Plan your organization structure before creating. Consider separating by business unit, product line, or team function.

* * *

## 6) Connect Entra ID Groups to GitHub Teams

_This is the "Golden Path" for automated permissions management — users added to Entra groups automatically get GitHub team membership._

### Navigation Path (GitHub)

1. Profile picture → **Your organizations** → Select the organization created in Step 5
2. **Teams** tab → **New team**
3. Configure the team:

| Field | Value |
| --- | --- |
| **Team name** | e.g., `developers`, `platform-engineers` |
| **Description** | Optional but recommended |
| **Identity Provider Groups** | Select the synced Entra group from dropdown (e.g., `M365-Developers`) |
4. Click **Create team**

### Important Constraints

> ⚠️ **No Nested Teams:** When a team is linked to an IdP group, you cannot nest it under other teams. The Entra group membership flattens into the GitHub team.

> ⚠️ **Nested Groups in Entra:** Microsoft Entra ID does not support provisioning nested groups. If you have Group A containing Group B, only direct members of Group A will sync — members of Group B will not be included unless they're also direct members of Group A.

* * *

## 7) Connect Azure Subscription (Billing)

_Required for metered billing: Copilot, Actions minutes beyond included amounts, Packages storage, GitHub Advanced Security, and Codespaces._

### Prerequisites Check

Before proceeding, ensure you have:

- \[ \] Tenant-wide admin consent capability in Azure (or admin consent workflow configured)
- \[ \] Owner permissions on the target Azure Subscription

### Navigation Path (GitHub)

1. Go to your enterprise: `https://github.com/enterprises/{slug}/settings`
2. Click **Billing & Licensing** tab
3. Click **Payment information**
4. Scroll to the bottom of the page
5. To the right of **"Metered billing via Azure"**, click **Add Azure Subscription**

### Azure Connection Flow

6. Sign in with your Microsoft account (must have tenant-wide admin consent rights)
7. Review the **"Permissions requested"** prompt for the GitHub SPV (Subscription Permission Validation) app
8. If you agree, click **Accept**

> ⚠️ **Troubleshooting:** If you see "You need admin approval" instead of the permissions prompt, work with your Azure AD Global Administrator to either:
> 
> - Grant consent on your behalf, OR
> - Configure an [admin consent workflow](https://learn.microsoft.com/en-us/azure/active-directory/manage-apps/configure-admin-consent-workflow)
9. Under **"Select a subscription"**, choose the Azure Subscription ID to connect
10. Check the confirmation box
11. Click **Connect**

### Verification

- ✅ The Azure Subscription ID should now appear under "Payment information"
- ✅ You can now enable metered services like Copilot and GHAS

* * *

## 8) Enable Copilot & Assign Seats

_Two methods available: Organization Teams (traditional) or Enterprise Teams (new, public preview)_

### Phase A: Enable Copilot at Enterprise Level

1. Enterprise Settings → **Policies** → **Copilot**
2. Under **"Access to Copilot"**, select **Enabled** (or "Allowed for all organizations" / "Allowed for specific organizations")
3. Configure additional policies:

| Policy | Recommendation |
| --- | --- |
| **Suggestions matching public code** | Consult legal counsel — Block or Allow based on IP policy |
| **Copilot Chat** | Enable for full functionality |
| **Copilot in the CLI** | Enable as needed |
4. Click **Save**

> ⚠️ **Important:** If this policy is not enabled, you cannot assign Copilot seats at the organization level.

### Phase B: Assign Seats

#### Option 1: Via Organization Teams (Traditional Method)

1. Navigate to your organization → **Settings**
2. Left sidebar: **Copilot** → **Access**
3. If prompted, click **Allow this organization to assign seats**
4. Click **Start adding seats**
5. Select **Purchase for selected members**
6. Click the **Users and teams** tab
7. Search for and select the team created in Step 6 (e.g., `developers`)
8. Click **Continue to purchase** → **Purchase seats**

#### Option 2: Via Enterprise Teams (Public Preview - September 2025+)

_This method allows assigning Copilot licenses at the enterprise level, without requiring organization membership._

1. Enterprise Settings → **People** → **Enterprise teams**
2. Create or select an Enterprise Team (can be synced with IdP groups via SCIM)
3. Enterprise Settings → **Billing & Licensing** → **Licensing**
4. Click **Copilot Business** (or Enterprise)
5. Under **User management**, click **Add seats**
6. Select **Add enterprise teams**
7. Search for and select your Enterprise Team
8. Click **Add team**

> 💡 **When to use Enterprise Teams:**
> 
> - Users who need Copilot but not full GitHub Enterprise Cloud licenses
> - Simplified management for large enterprises
> - Direct IdP group synchronization at enterprise level

### Result (Zero-Touch Provisioning)

When configured correctly:

1. User is added to Entra ID group (e.g., `M365-Developers`)
2. SCIM automatically provisions their GitHub account
3. User is automatically added to the linked GitHub Team
4. Copilot license is automatically assigned via Team membership
5. User can immediately use Copilot in their IDE

* * *

## 9) Validation Checklist

Run through these checks to confirm successful setup:

| Check | How to Verify | Expected Result |
| --- | --- | --- |
| **OIDC SSO** | Have a provisioned user (not setup user) attempt to sign in | Redirects to Entra ID, successfully authenticates |
| **SCIM Provisioning** | Check Enterprise → People tab | Test users appear with `_shortcode` suffix |
| **Group Sync** | Check Organization → Teams | IdP group members appear in linked team |
| **Azure Billing** | Enterprise → Billing & Licensing → Payment information | Azure Subscription ID displayed |
| **Copilot Access** | User opens VS Code/IDE with GitHub Copilot extension | Copilot icon active, suggestions working |

### Troubleshooting Quick Reference

| Issue | Common Cause | Solution |
| --- | --- | --- |
| Users not provisioning | SCIM token expired or invalid | Regenerate PAT with `scim:enterprise` scope |
| SSO redirect fails | Entra app misconfigured | Verify OIDC app settings in Entra admin center |
| "Admin approval required" for Azure | Insufficient Azure AD permissions | Request tenant-wide admin consent |
| Copilot not activating | Policy not enabled at enterprise level | Enable in Enterprise → Policies → Copilot |
| Team membership not syncing | Nested groups in Entra | Flatten group structure or add users directly |

* * *

## Appendix A: Key URLs

| Resource | URL |
| --- | --- |
| Enterprise Settings | `https://github.com/enterprises/{slug}/settings` |
| Microsoft Entra Admin Center | `https://entra.microsoft.com` |
| GitHub Support Portal | `https://support.github.com` |
| GitHub Status | `https://www.githubstatus.com` |

* * *

## Appendix B: Source Documentation

| #   | Document | URL |
| --- | --- | --- |
| 1   | Connecting an Azure subscription | [https://docs.github.com/enterprise-cloud@latest/billing/managing-the-plan-for-your-github-account/connecting-an-azure-subscription](https://docs.github.com/enterprise-cloud@latest/billing/managing-the-plan-for-your-github-account/connecting-an-azure-subscription) |
| 2   | Setup user 2FA requirement (Jan 2025) | [https://github.blog/changelog/2025-01-17-setup-user-for-emu-enterprises-requires-2fa-or-use-of-a-recovery-code/](https://github.blog/changelog/2025-01-17-setup-user-for-emu-enterprises-requires-2fa-or-use-of-a-recovery-code/) |
| 3   | Configuring SCIM provisioning for EMU | [https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users](https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/configuring-scim-provisioning-for-users) |
| 4   | SCIM REST API documentation | [https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/provisioning-users-and-groups-with-scim-using-the-rest-api](https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/provisioning-users-and-groups-with-scim-using-the-rest-api) |
| 5   | Configuring OIDC for EMU | [https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users](https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/configuring-authentication-for-enterprise-managed-users/configuring-oidc-for-enterprise-managed-users) |
| 6   | Managing team memberships with IdP groups | [https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/managing-team-memberships-with-identity-provider-groups](https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/provisioning-user-accounts-with-scim/managing-team-memberships-with-identity-provider-groups) |
| 7   | Copilot policies for enterprise | [https://docs.github.com/enterprise-cloud@latest/copilot/managing-copilot/managing-copilot-for-your-enterprise/managing-policies-and-features-for-copilot-in-your-enterprise](https://docs.github.com/enterprise-cloud@latest/copilot/managing-copilot/managing-copilot-for-your-enterprise/managing-policies-and-features-for-copilot-in-your-enterprise) |
| 8   | Microsoft Entra OIDC provisioning tutorial | [https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-oidc-provisioning-tutorial](https://learn.microsoft.com/en-us/entra/identity/saas-apps/github-enterprise-managed-user-oidc-provisioning-tutorial) |
| 9   | Enterprise Teams (Sept 2025) | [https://github.blog/changelog/2025-09-04-manage-copilot-and-users-via-enterprise-teams-in-public-preview/](https://github.blog/changelog/2025-09-04-manage-copilot-and-users-via-enterprise-teams-in-public-preview/) |
| 10  | Downloading enterprise recovery codes | [https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/managing-recovery-codes-for-your-enterprise/downloading-your-enterprise-accounts-single-sign-on-recovery-codes](https://docs.github.com/enterprise-cloud@latest/admin/managing-iam/managing-recovery-codes-for-your-enterprise/downloading-your-enterprise-accounts-single-sign-on-recovery-codes) |

* * *

## Revision History

| Date | Version | Changes |
| --- | --- | --- |
| December 2025 | 2.0 | Verified against current documentation; updated OIDC navigation path; clarified Azure permissions; added Enterprise Teams option for Copilot; fixed source references |

<br>