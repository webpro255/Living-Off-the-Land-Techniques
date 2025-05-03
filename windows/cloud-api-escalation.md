# ☁️ Cloud API Shadow Admin Abuse - Silent Privilege in the Cloud

**Cloud API Shadow Admin Abuse** is a stealthy privilege escalation technique in cloud environments like Azure AD and AWS. Instead of modifying visible roles or groups, attackers manipulate **API permissions** or **service principals** to gain full access without showing up as admins in the GUI.

> ⚠️ Used by advanced threat actors like **Nobelium (SolarWinds)** to persist in cloud environments while avoiding detection. This is how you stay admin — without looking like one.

---

## 🛠️ What It Does
- Modifies OAuth app permissions (Microsoft Graph, AWS IAM, etc.)
- Grants delegated or app-level access to sensitive APIs
- Doesn’t require the user to be in admin groups
- Avoids standard audit trails and GUI visibility

---

## 💣 Example: Azure AD Shadow Admin

### 1. Attacker Registers a Malicious App
```powershell
az ad app create --display-name "Updater" --identifier-uris "https://updater.app"
```

### 2. Assigns OAuth Permissions
```powershell
az ad app permission add --id <app-id> --api 00000003-0000-0000-c000-000000000000 --api-permissions Directory.ReadWrite.All=Role
```
✅ App now has full control over Azure AD — even if user isn’t an admin

---

### 3. Grants Consent (If Compromised Admin)
```powershell
az ad app permission grant --id <app-id> --api 00000003-0000-0000-c000-000000000000
```

Or via `Microsoft.Graph` PowerShell module for full silent API abuse

---

## AWS Variant
- Attach permissions to a trusted Lambda or IAM Role
- Abuse AssumeRole or STS to impersonate trusted services
- Shadow privilege persists even if attacker leaves — **because the service still has it**

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| High-privilege OAuth apps not used by legitimate services | Audit Graph API and consented permissions regularly |
| Service Principals with Graph access not tied to admins | Cross-check `roleAssignments` against GUI-visible users |
| AWS: STS AssumeRole anomalies | Monitor CloudTrail for unusual `AssumeRole` patterns

---

## 🔐 Mitigation Strategies
- Require admin approval for all OAuth app permissions
- Audit all service principals and app registrations regularly
- Use Conditional Access to restrict API scope
- Alert on changes to Graph API or AWS IAM permission structures

---

> 🧠 The cloud doesn’t care what the UI shows — it listens to APIs. Shadow Admins don’t wear a badge — they hold the keys silently. This is **how attackers vanish into the cloud.**
