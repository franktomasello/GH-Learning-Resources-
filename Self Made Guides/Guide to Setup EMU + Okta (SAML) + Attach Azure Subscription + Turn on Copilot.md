# 🚀 GitHub Enterprise Managed Users (EMU) Setup Guide (Okta SAML)

**Complete end-to-end runbook for configuring EMU with Okta (SAML), Azure billing, and GitHub Copilot**

---

## 📋 Overview

This guide walks through setting up a new GitHub Enterprise Cloud (GHEC) with Enterprise Managed Users (EMU), including:
- ✅ EMU with Okta (SAML SSO)
- ✅ SCIM provisioning for user lifecycle management
- ✅ Azure subscription attachment for billing
- ✅ GitHub Copilot enablement
- ✅ Organization structure guidance

**Hosting Options:** EMU can be hosted on GitHub.com or (for data residency) on a customer subdomain of GHE.com. Setup is similar, but SAML/SCIM URLs and Okta app selection differ.

---

## 0️⃣ Prerequisites Checklist

### Required Items

Before beginning, ensure you have:

| Requirement | Status |
|-------------|--------|
| EMU Enterprise Created — A new enterprise with EMU enabled | ☐ |
| Setup User — SHORTCODE_admin created by GitHub, with password set and 2FA enabled | ☐ |
| Okta Admin Access — Ability to create App Integrations and configure SAML SSO + SCIM | ☐ |
| Azure Subscription — Subscription ID and someone who can grant tenant-wide admin consent | ☐ |

> 💡 **Note:** SCIM provisioning is required for EMU to manage user lifecycle and account creation.

---

## 1️⃣ Create & Configure the EMU Setup User

### Process

1. GitHub emails an invite to set the password for SHORTCODE_admin
2. In a private/incognito window:
   - Set password
   - Enable 2FA
   - Save recovery codes securely

### Important Notes

> ⚠️ **Setup User Purpose:** This account is primarily for SCIM provisioning via token and recovery scenarios. Day-to-day enterprise administration should be done with provisioned managed user accounts.

> 🚨 **Email Conflict:** If the provided email address is already associated as a primary email with an existing GitHub account, the activation link will not work. Modify the existing account's primary email first.

---

## 2️⃣ Create the Okta Application

### Navigation

```
Okta Admin Console
  → Applications
    → Applications
      → Browse App Catalog   (or "Browse App Integration Catalog")
        → Search: "GitHub Enterprise Managed User"
          → Select the correct integration
            → Add Integration
```

### Select the Correct Okta Integration

- **For GitHub.com hosted EMU:** install GitHub Enterprise Managed User
- **For GHE.com hosted EMU (Data Residency):** install GitHub Enterprise Managed User - GHE.com

### SAML Configuration

1. Navigate to the application Sign On tab
2. In the SAML settings section, set Enterprise Name to your enterprise slug (example: acme)
3. Save

**Navigation Path:**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Sign On
          → (SAML 2.0 section)
            → Edit
              → Enterprise Name
                → Save
```

### Download Required Items (Okta IdP values)

From the Okta application:
1. **Sign on URL** (IdP Sign-On URL) — Copy this value
2. **Issuer** — Copy this value
3. **Signing certificate** (X.509) — Copy/download the certificate contents

**Navigation Path:**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Sign On
          → (SAML 2.0 section)
            → View SAML setup instructions   (or "More details")
```

### SAML Values (GitHub SP values)

#### For GitHub.com Hosted EMU

| Field | Value |
|-------|-------|
| Identifier (Entity ID) | `https://github.com/enterprises/YOUR_ENTERPRISE` |
| Reply URL (ACS URL) | `https://github.com/enterprises/YOUR_ENTERPRISE/saml/consume` |
| Sign-on URL | `https://github.com/enterprises/YOUR_ENTERPRISE/sso` |

#### For GHE.com Hosted EMU (Data Residency)

| Field | Value |
|-------|-------|
| Identifier (Entity ID) | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN` |
| Reply URL (ACS URL) | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN/saml/consume` |
| Sign-on URL | `https://SUBDOMAIN.ghe.com/enterprises/SUBDOMAIN/sso` |

> ⚠️ **Critical:** Ensure your Identifier format matches GitHub exactly and does not include a trailing slash.

### Assign Users (for SSO testing)

Assign at least one user (or group) to the Okta application so you can validate SSO.

**Navigation Path:**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Assignments
          → Assign
            → Assign to People (or Assign to Groups)
              → (Select user/group)
                → Assign
                  → Done
```

---

## 3️⃣ Enable SAML SSO in GitHub

> ⚠️ **Warning:** Enabling SAML impacts how members authenticate. Ensure you have recovery codes stored for break-glass access.

### Navigation Path (GitHub UI option A — matches many Enterprise Settings layouts)

```
GitHub (as SHORTCODE_admin)
  → Profile picture (top-right)
    → Your enterprises
      → Select your enterprise
        → Settings
          → Authentication security
```

### Navigation Path (GitHub UI option B — EMU layout seen in some enterprises)

```
GitHub (as SHORTCODE_admin)
  → Profile picture (top-right)
    → Enterprise
      → Identity provider
        → Single sign-on configuration
```

Use Option A if you see Settings → Authentication security.
Use Option B if you see an Identity provider top navigation.

### Configuration Steps

1. Locate the SAML single sign-on section (or Add SAML configuration)
2. Enable SAML (e.g., check Require SAML authentication or click Add SAML configuration)
3. Enter the following values from Okta:
   - **Sign on URL:** Paste the Sign on URL (IdP Sign-On URL) from Okta
   - **Issuer:** Paste the Issuer from Okta
   - **Public certificate:** Paste the contents of the Okta Signing certificate (X.509)
4. Click Save (or Save SAML settings)

> 🔐 **Critical:** Before enabling or immediately after enabling SAML, download and securely store your enterprise SSO recovery codes. These are essential for break-glass scenarios if your IdP becomes unavailable.

---

## 4️⃣ Configure SCIM Provisioning

SCIM handles user creation and deactivation in EMU. This requires creating a token in GitHub and configuring provisioning in Okta.

### 4A — Create the SCIM Token in GitHub

The token must be created as the setup user with specific requirements:

**Token Requirements:**
- ✓ Scope: `scim:enterprise`
- ✓ Expiration: No expiration
- ✓ Created by: SHORTCODE_admin

**Creation Steps:**

```
GitHub (as SHORTCODE_admin)
  → Profile picture
    → Settings
      → Developer settings
        → Personal access tokens
          → Tokens (classic)
            → Generate new token (classic)
```

**Configuration:**
- **Note:** "Okta SCIM Provisioning" (or similar descriptive name)
- **Expiration:** No expiration
- **Scope:** Select `scim:enterprise` only

> 🔑 **Important:** Copy the token immediately after generation. You won't be able to see it again.

### 4B — Configure Provisioning in Okta

**Navigation Path:**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Provisioning
          → Integration
            → Edit
              → Configure API Integration
```

**Configuration:**
1. Enable API Integration
2. Under API Token, paste the PAT created in step 4A
3. If prompted for SCIM base URL / tenant URL, use the values below (environment-specific)
4. Click Test API Credentials
5. Click Save

**Tenant / Base URL Values (when Okta requires them):**

| Environment | SCIM URL |
|-------------|----------|
| GitHub.com | `https://api.github.com/scim/v2/enterprises/YOUR_ENTERPRISE` |
| GHE.com | `https://api.SUBDOMAIN.ghe.com/scim/v2/enterprises/SUBDOMAIN` |

> 📌 **Note:** Okta's Import Groups setting is not supported by GitHub for EMU and does not affect behavior.

### 4C — Configure Attribute Mappings

Okta generally provides correct default mappings for the GitHub EMU integration. If you need to review/edit mappings:

**Navigation Path (view/edit mappings):**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Provisioning
          → To App
            → Edit
              → (Scroll) Attribute Mappings
                → (Click) Mappings / Go to Profile Editor (label varies)
```

- Review default user attribute mappings (typically sufficient for most deployments).
- Ensure provisioning is enabled under To App actions (next step).

### 4D — Assign Users/Groups for Provisioning

1. Navigate to Assignments in the Okta app
2. Assign users and/or groups to the application
3. Okta will SCIM-provision these members into the EMU enterprise

**Navigation Path:**

```
Okta Admin Console
  → Applications
    → Applications
      → GitHub Enterprise Managed User
        → Assignments
          → Assign
            → Assign to People / Assign to Groups
              → (Select users/groups)
                → Assign
                  → Done
```

**Provisioning Notes:**

> 📌 **Important Constraints:**
> - Do not assign more than 1,000 users per hour to avoid rate limits.

---

## 5️⃣ Attach Azure Subscription for Billing

Connect your Azure subscription so GitHub usage (Copilot, Actions, Codespaces, etc.) is billed through Azure.

### Prerequisites

- ✓ Owner of the GitHub enterprise account
- ✓ Azure Subscription ID
- ✓ Azure user who can provide tenant-wide admin consent

### Configuration Steps

**Navigation Path:**

```
GitHub
  → Your enterprise (https://github.com/enterprises/YOUR_ENTERPRISE)
    → Billing & licensing
      → Payment information
        → Metered billing via Azure
          → Add Azure Subscription
```

**Process:**
1. Click Add Azure Subscription
2. Sign in to your Microsoft account
3. Review the "Permissions requested" prompt
4. Click Accept
5. Select your Azure Subscription ID
6. Check the confirmation box
7. Click Connect

> 💡 **Admin Consent:** If you don't see a "Permissions requested" prompt and instead see a message about needing admin approval, you may need to configure an admin consent workflow in Azure or work with your Azure AD global administrator.

---

## 6️⃣ Enable GitHub Copilot

### 6A — Enable at Enterprise Level

With Azure billing connected via metered billing, Copilot usage will be billed through your Azure subscription.

**Navigation Path:**

```
GitHub Enterprise Settings
  → AI controls (top navigation)
    → Copilot (sidebar)
```

**Access Management Configuration:**

Under Access management, choose:
- **Disabled** — No organizations can use Copilot
- **All organizations** — Enable for all organizations in the enterprise
- **Specific organizations** — Select which organizations can use Copilot

Select the Copilot tier (Business or Enterprise) for each enabled organization.

### 6B — Configure Copilot Policies

1. Navigate to the Policies tab under AI controls → Copilot
2. Configure policies for:
   - Suggestions matching public code (Allowed/Blocked)
   - Copilot in GitHub.com
   - Copilot Chat in the IDE
   - Copilot in the CLI
   - Other feature policies

**Policy Options:**

For each policy, select:
- **Enabled/Allowed** — Feature is on for all organizations
- **Disabled/Blocked** — Feature is off for all organizations
- **No policy** — Delegate decision to organization owners

### 6C — Assign Copilot Seats

After enabling at the enterprise level:

```
Organization Settings
  → Copilot
    → Access
      → Add members or enable for all
```

Organization owners can assign Copilot seats to individual members or teams.

---

## 7️⃣ Organization Structure Guidance

Organizations are boundaries for ownership, settings, and repository visibility patterns. EMU provides centralized identity and lifecycle management across all organizations.

### Recommended Patterns

**📐 Design Principles**

"Few orgs" bias: Keep organization count low unless you have genuine separation needs:
- Compliance boundaries
- Distinct admin models
- Legal entity separation

**🏢 Common Structures**

**Org-per-business-unit:**
- Use when autonomy differs
- Separate admins and policies
- Different billing/cost centers

**Org-per-environment:**
- Only if required (e.g., regulated prod code vs everything else)
- Otherwise, teams and repositories are usually sufficient

### Inside an Organization

**Teams:**
- Use Teams for repository access control
- Use Okta groups for consistent membership (synced via SCIM)
- Start with a small set of "platform" teams:
  - Developers
  - Maintainers
  - Security Champions
  - CI Admins
- Grow organically from there

**Repository Visibility:**
- EMU provides an Internal visibility type
- Perfect for InnerSourcing within your enterprise
- Visible to all enterprise members, but not public

---

## 📝 Quick Reference Worksheet

### GitHub.com Hosted EMU

```
SAML Entity ID:     https://github.com/enterprises/{ENTERPRISE_SLUG}
SAML ACS URL:       https://github.com/enterprises/{ENTERPRISE_SLUG}/saml/consume
SAML SSO URL:       https://github.com/enterprises/{ENTERPRISE_SLUG}/sso
SCIM Tenant URL:    https://api.github.com/scim/v2/enterprises/{ENTERPRISE_SLUG}
```

### GHE.com (Data Residency) Hosted EMU

```
SAML Entity ID:     https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}
SAML ACS URL:       https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}/saml/consume
SAML SSO URL:       https://{SUBDOMAIN}.ghe.com/enterprises/{SUBDOMAIN}/sso
SCIM Tenant URL:    https://api.{SUBDOMAIN}.ghe.com/scim/v2/enterprises/{SUBDOMAIN}
```

---

## ✅ Pre-Flight Checklist

### Before Starting

- Enterprise slug/subdomain: __________________
- Setup user credentials stored securely
- Setup user 2FA enabled with recovery codes saved
- Okta admin with privileges to create/configure app integrations
- Azure Subscription ID (for billing): __________________
- Azure admin who can grant tenant-wide consent

### PAT Token Checklist

- Created as setup user (SHORTCODE_admin)
- Scope: `scim:enterprise`
- Expiration: No expiration
- Token stored securely

---

## 👥 Example Initial Group Model

| Group Name | Enterprise Role | Purpose |
|------------|-----------------|---------|
| GitHub-Enterprise-Owners | Enterprise Owner | Full admin access |
| GitHub-Developers | Member | Standard developer access |
| GitHub-Security | Member | Security champions |
| GitHub-CI-Admins | Member | CI/CD pipeline admins |

---

## 🎯 Success Criteria

After completing this guide, you should have:
- ✅ EMU enterprise fully configured with Okta authentication
- ✅ SCIM provisioning active for automated user lifecycle management
- ✅ Azure subscription connected for metered billing
- ✅ GitHub Copilot enabled and configured
- ✅ Initial organization structure established
- ✅ First users provisioned and able to access GitHub

---

*Last updated: January 2026*
