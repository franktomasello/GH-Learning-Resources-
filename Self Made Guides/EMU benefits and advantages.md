## EMU benefits and advantages 

### What is Github EMU?

- GitHub EMU is a deployment model for GitHub Enterprise Cloud where every account inside the tenant is created and owned by your corporate identity provider (IDP) — Microsoft  Entra ID, Okta, and PingFederate

### What is the alternative to EMU?

- In the alternative “Enterprise with personal accounts” model, employees bring a normal GitHub account that they also use on public GitHub. You can still enforce SAML SSO, but the account ultimately belongs to the user, not the company.

### What are the key benefits of EMU?

- Zero-touch onboarding/offboarding. The accounts are auto-created, suspended, or deleted when the IDP record changes 
    - Eliminates “orphaned” access when staff leave and ends manual cleanup work

- Users can sign in **only** through corporate SSO; no passwords, PATs, or 2FA secrets live on GitHub.
- Managed users **cannot create public content or collaborate outside the tenant**
    - Keeps Intellectual Property walled off 
- Enterprise-wide policies (PAT restrictions, Actions rules, audit log streaming, IdP Conditional Access) apply uniformly because every user is directory-backed
- The company, not the employee, owns usernames and email addresses shown on GitHub
- Employee GitHub identities are not exposed publicly; developers appear only inside the enterprise tenant

### Trade offs to be aware of

- **Open-source participation** – Developers must use a separate personal account for public OSS work, since EMU accounts are sandboxed.

- **Migration effort** – Moving an existing enterprise to EMU involves renaming accounts and re-linking contributions; plan for change-management.

### Personal Accounts are better for:

- Teams actively contributing to open source under the same identity

- Startups valuing developer autonomy

### Bottom line:

- Choose **EMU** if security, compliance, and centralized access control are your top priorities. Stick with **personal accounts** if open-source collaboration and minimal friction for developers matter more.

<br>

[https://emu-instructions.githubapp.com](https://emu-instructions.githubapp.com)