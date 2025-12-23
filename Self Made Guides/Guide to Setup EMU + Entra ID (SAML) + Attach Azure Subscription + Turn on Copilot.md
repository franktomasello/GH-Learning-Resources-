# Guide to Setup EMU + Entra ID (SAML) + Attach Azure Subscription + Turn on Copilot

Below is an end-to-end runbook for a new GitHub Enterprise Cloud (GHEC) Enterprise Managed Users (EMU) customer doing:

**EMU + Microsoft Entra ID (SAML SSO) + Attach Azure Subscription (Azure billing) + Turn on Copilot + org-structure guidance**

EMU can be hosted on GitHub.com or (for data residency) on a customer subdomain of GHE.com—the setup is similar, but SAML/SCIM URLs differ.

* * *

## 0) Prerequisites Checklist (Do These First)

### You Need:

- A new enterprise created with EMU enabled (this is a different "type" of enterprise)
- The setup user (`SHORTCODE_admin`) created by GitHub, with password set + 2FA enabled (store recovery codes)
- Entra admin access to create an Enterprise App and configure SAML SSO + Provisioning (SCIM). SCIM is required for lifecycle/user creation in EMU.
- (For Azure billing) the Azure Subscription ID and someone who can grant tenant-wide admin consent if prompted

* * *

## 1) Create / Access the EMU Enterprise + Setup User

### What Happens:

1. GitHub emails an invite to set the password for the setup user named `SHORTCODE_admin`
2. In a private/incognito window: set password → enable 2FA → save recovery codes

> **Note:** The setup user is mainly for SCIM provisioning via token and recovery scenarios; day-to-day enterprise admin should be done with provisioned managed user accounts.

> **Important:** If the email address provided is already associated as a primary email with an existing GitHub account, the activation link will not work. Modify the existing GitHub account's primary email first.

* * *

## 2) In Entra ID: Create the "GitHub Enterprise Managed User" Enterprise App (SAML)

### Click Path (Entra Admin Center):

1. Microsoft Entra admin center → Entra ID → **Enterprise applications**
2. **\+ New application** → Browse Azure AD Gallery → search **"GitHub Enterprise Managed User"** → Create/Add
3. In the app → **Single sign-on** → choose **SAML** → Edit **"Basic SAML Configuration"**

### Basic SAML Configuration Values

Use the correct values for where they're hosted:

#### If hosted on GitHub.com:

| Field | Value |
| --- | --- |
| Identifier (Entity ID) | `https://github.com/enterprises/YOUR_ENTERPRISE` |
| Reply URL (ACS URL) | `https://github.com/enterprises/YOUR_ENTERPRISE/saml/consume` |
| Sign-on URL (optional, for SP-initiated) | `https://github.com/enterprises/YOUR_ENTERPRISE/sso` |

#### If hosted on GHE.com (data residency subdomain):

| Field | Value |
| --- | --- |
| Identifier (Entity ID) | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN` |
| Reply URL (ACS URL) | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN/saml/consume` |
| Sign-on URL (optional, for SP-initiated) | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN/sso` |

> **Important:** The Identifier format is different from the application's suggested format—ensure the Identifier doesn't contain a trailing slash.

### Then in Entra:

1. Download the **SAML certificate (Base64)**
2. Copy the **Login URL** and **Microsoft Entra Identifier** (you'll paste these into GitHub in the next section)
3. Go to **Users and Groups** and assign one or more users with the **Enterprise Owner** role

* * *

## 3) In GitHub: Enable/Configure SAML SSO for EMU (Authentication)

### Click Path (GitHub):

1. Sign in as `SHORTCODE_admin`
2. Top-right profile picture → **Your enterprises** → Select your enterprise
3. In the sidebar → **Settings** → **Authentication security**

### What You Configure Here:

1. Under Authentication security, locate the SAML single sign-on section
2. Check **Require SAML authentication**
3. Paste values from Entra:
    - **Sign on URL**: The Login URL from Entra
    - **Issuer**: The Microsoft Entra Identifier from Entra
    - **Public certificate**: Paste the contents of the Base64 certificate downloaded from Entra
4. Click **Save**

> **Critical:** Before enabling SAML, download and securely store your **enterprise SSO recovery codes**. These are essential for break-glass scenarios if your IdP becomes unavailable.

* * *

## 4) Configure SCIM Provisioning

SCIM is what actually creates/deactivates users in EMU.

### 4A) Create the SCIM Token (PAT) in GitHub

This token must be created as the setup user and must:

- Have **`scim:enterprise`** scope
- Have **no expiration**

#### GitHub Click Path (PAT Classic):

1. Sign in as `SHORTCODE_admin`
2. Top-right profile picture → **Settings** → **Developer settings**
3. **Personal access tokens** → **Tokens (classic)** → **Generate new token (classic)**
4. Configure:
    - **Note**: Descriptive name (e.g., "Entra SCIM Provisioning")
    - **Expiration**: No expiration
    - **Scope**: Select `scim:enterprise`
5. Generate and copy the token immediately (you won't see it again)

### 4B) Configure Provisioning in Entra

#### Click Path (Entra):

1. Entra admin center → Entra ID → **Enterprise applications** → **GitHub Enterprise Managed User**
2. **Provisioning** tab → set **Provisioning Mode** = **Automatic**
3. Under **Admin Credentials**:
    - **Tenant URL**: (use the appropriate URL below)
    - **Secret Token**: The PAT you created
4. Click **Test Connection** → **Save**

#### Tenant URL Values:

| Environment | Tenant URL |
| --- | --- |
| GitHub.com | `https://api.github.com/scim/v2/enterprises/YOUR_ENTERPRISE` |
| GHE.com | `https://api.SUBDOMAIN.ghe.com/scim/v2/enterprises/SUBDOMAIN` |

### 4C) Configure Attribute Mappings

1. Under the **Mappings** section, select **Synchronize Microsoft Entra users to GitHub Enterprise Managed User**
2. Review the default attribute mappings—they typically work for most scenarios
3. Ensure **Provisioning Status** is set to **On** under Settings

### 4D) Assign Users/Groups to Provision Accounts

1. In Entra, go to the Enterprise App → **Users and groups**
2. Assign users and/or groups to the Enterprise App
3. Entra will SCIM-provision those members into the EMU enterprise

> **Notes:**
> 
> - Entra does not provision nested groups
> - The provisioning cycle runs approximately every 40 minutes; use "Provision on demand" for immediate provisioning
> - Do not assign more than 1,000 users per hour to avoid rate limits

> **Warning:** GitHub explicitly warns against certain mixed-IdP combinations. The combination of Okta and Entra ID for SSO and SCIM (in either order) is explicitly **not supported**.

* * *

## 5) Attach the Azure Subscription (Azure Billing)

This is the "connect Azure subscription" step so GitHub usage (including Copilot, Actions, Codespaces, etc.) can be billed through Azure.

### Prerequisites:

- You must be an owner of the GitHub enterprise account
- You must know your Azure Subscription ID
- You must be logged into Azure as a user who can provide tenant-wide admin consent (or work with an Azure AD global administrator)

### Click Path (GitHub):

1. Go to your enterprise: `https://github.com/enterprises/YOUR_ENTERPRISE` (or `https://SUBDOMAIN.ghe.com` for data residency)
2. In the sidebar → **Billing & licensing** (or click the **Billing** tab at top)
3. Click **Payment information**
4. Scroll to **Metered billing via Azure** → Click **Add Azure Subscription**
5. Sign in to your Microsoft account
6. Review the "Permissions requested" prompt → Click **Accept**
7. Select the Azure Subscription ID you want to connect
8. Check the confirmation box and click **Connect**

> **Note:** If you don't see a "Permissions requested" prompt and instead see a message indicating you need admin approval, you may need to configure an admin consent workflow in Azure or work with your Azure AD global administrator.

* * *

## 6) Turn on Copilot (Enterprise-Level)

### 6A) Enable Copilot for the Enterprise

If connected to Azure billing via metered billing, Copilot usage will be billed through your Azure subscription.

#### Click Path (GitHub):

1. Go to your enterprise settings
2. In the top navigation → Click **AI controls**
3. In the sidebar → Click **Copilot**
4. Under **Access management**, choose:
    - **Disabled**: No organizations can use Copilot
    - **All organizations**: Enable for all organizations in the enterprise
    - **Specific organizations**: Select which organizations can use Copilot
5. Select the **Copilot tier** (Business or Enterprise) for each enabled organization

### 6B) Configure Copilot Policies

1. Still under **AI controls** → **Copilot**
2. Click the **Policies** tab to configure:
    - Suggestions matching public code (Allowed/Blocked)
    - Copilot in GitHub.com
    - Copilot Chat in the IDE
    - Copilot in the CLI
    - And other feature policies
3. For each policy, select:
    - **Enabled/Allowed**: Feature is on for all organizations
    - **Disabled/Blocked**: Feature is off for all organizations
    - **No policy**: Delegate decision to organization owners

### 6C) Assign Copilot Seats

After enabling at the enterprise level:

1. Organization owners can assign Copilot seats to individual members or teams
2. Navigate to: Organization → **Settings** → **Copilot** → **Access**
3. Add members or enable for all current and future members

* * *

## 7) Org Structure Guidance

A simple way to guide the customer: orgs are boundaries for ownership + settings + repo visibility patterns, and EMU gives you centralized identity + lifecycle across all orgs.

### Good Default Patterns:

- **"Few orgs" bias**: Keep org count low unless you have real separation needs (compliance boundary, distinct admin model, legal entity separation)
- **Org-per-business-unit**: When autonomy differs (separate admins, different policies, separate billing/cost centers)
- **Org-per-environment**: Only if required (e.g., regulated prod code vs everything else)—otherwise teams/repos are usually enough

### Inside an Org:

- Use **Teams** for repo access control; use **Entra groups** for consistent membership (SCIM)
- Start with a small set of "platform" teams (e.g., developers, maintainers, security-champions, ci-admins) and grow from there
- EMU provides an **Internal** repository visibility type—useful for InnerSourcing within your enterprise

* * *

## Quick Reference: Copy/Paste Worksheet

### For GitHub.com Hosted EMU:

```
SAML Entity ID:     https://github.com/enterprises/{ENTERPRISE_SLUG}
SAML ACS URL:       https://github.com/enterprises/{ENTERPRISE_SLUG}/saml/consume
SAML SSO URL:       https://github.com/enterprises/{ENTERPRISE_SLUG}/sso
SCIM Tenant URL:    https://api.github.com/scim/v2/enterprises/{ENTERPRISE_SLUG}
```

### For GHE.com (Data Residency) Hosted EMU:

```
SAML Entity ID:     https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}
SAML ACS URL:       https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}/saml/consume
SAML SSO URL:       https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}/sso
SCIM Tenant URL:    https://api.{SUBDOMAIN}.ghe.com/scim/v2/enterprises/{SUBDOMAIN}
```

### Checklist Before Starting:

- \[ \] Enterprise slug/subdomain: \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
- \[ \] Setup user credentials stored securely
- \[ \] Setup user 2FA enabled with recovery codes saved
- \[ \] Entra admin with Application Administrator or higher role
- \[ \] Azure Subscription ID (for billing): \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
- \[ \] Azure admin who can grant tenant-wide consent

### PAT Token Checklist:

- \[ \] Created as setup user (`SHORTCODE_admin`)
- \[ \] Scope: `scim:enterprise`
- \[ \] Expiration: No expiration
- \[ \] Token stored securely

### Initial Group Model (Example):

| Group Name | Enterprise Role | Purpose |
| --- | --- | --- |
| GitHub-Enterprise-Owners | Enterprise Owner | Full admin access |
| GitHub-Developers | Member | Standard developer access |
| GitHub-Security | Member | Security champions |
| GitHub-CI-Admins | Member | CI/CD pipeline admins |

* * *

<br>

<br>

<br>