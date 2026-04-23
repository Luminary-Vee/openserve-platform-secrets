# 🛠 Azure Key Vault Troubleshooting Guide

## 1. Purpose

This guide provides standardized troubleshooting procedures for Azure Key Vault integration issues across App Service environments using Managed Identity and Key Vault references.

It enables rapid diagnosis and resolution of authentication, configuration, and runtime failures.

---

## 2. Key Vault InvalidAuthorization (Signature Failure)

### Error

{
"code": 403,
"status": "Forbidden",
"errorCode": "InvalidAuthorization",
"errorMsg": "An invalid signature was specified in the Authorization header"
}


### Meaning
Authentication to Azure Key Vault failed due to an invalid signature.

### Common Causes
- Managed Identity not enabled
- Missing RBAC role assignment
- RBAC propagation delay
- Incorrect Key Vault reference format

### Resolution
- Enable Managed Identity on App Service
- Assign role: **Key Vault Secrets User**
- Verify reference format:


@Microsoft.KeyVault(SecretUri=https://<vault-name>.vault.azure.net/secrets/<secret-name>/)


- Restart App Service
- Wait 5–10 minutes for RBAC propagation

### Prevention
- Assign RBAC before deployment
- Avoid restarting immediately after permission changes

---

## 3. Authentication Failure (NOAUTH / WRONGPASS / Parser Errors)

### Errors

NOAUTH Authentication required
WRONGPASS invalid username-password pair
at parseError (/node_modules/redis-parser/lib/parser.js)


### Meaning
Authentication failed. The client may throw parser-level errors due to malformed AUTH input.

### Symptoms
- For Instance Redis connects but commands fail
- Repeated NOAUTH / WRONGPASS
- Logs reference `redis-parser` or `parseError`

### Root Cause
- Key Vault secret not resolved
- Secret URI passed instead of actual value
- Invalid credential format
- App initialized before secrets load

### Critical Indicator
If logs show:

auth: https://<vault-name>.vault.azure.net/secrets/<secret-name>/<version>


👉 This confirms **Key Vault URI is being passed instead of the secret value**

### Resolution
- Ensure Key Vault reference resolves correctly
- Confirm application receives actual secret value
- Validate Redis config binding
- Ensure secrets load before Redis initialization
- Restart App Service

### Prevention
- Never pass Key Vault URI to Redis
- Add startup validation for secrets
- Ensure proper initialization order

---

## 4. Key Vault Reference Not Resolving

### Example

@Microsoft.KeyVault(SecretUri=...)


### Meaning
App Service is not resolving the Key Vault reference.

### Causes
- Managed Identity not enabled
- Missing RBAC permissions
- Incorrect SecretUri format
- Key Vault network restrictions

### Resolution
- Enable Managed Identity
- Assign RBAC role
- Verify SecretUri format
- Check firewall / private endpoint settings
- Restart App Service

---

## 5. APIM InvalidAuthorization (Authorization Header Failure)

### Error

{
"code": 403,
"status": "Forbidden",
"errorCode": "InvalidAuthorization",
"errorMsg": "An invalid signature was specified in the Authorization header"
}


### Meaning
Request to Azure API Management (APIM) failed due to an invalid or malformed Authorization header.

### Symptoms
- HTTP 403 from API calls
- Request fails before backend
- No backend logs triggered

### Common Causes
- Incorrect API key / subscription key
- Malformed or expired token
- Wrong Authorization header format
- Key Vault secret not resolved
- Key Vault URI passed instead of actual value

### Critical Pattern

Authorization: https://<vault-name>.vault.azure.net/secrets/...


👉 Indicates **Key Vault URI used instead of actual token**

### Resolution
- Verify correct header format:
  - `Authorization: Bearer <token>`
  - `Ocp-Apim-Subscription-Key: <key>`
- Confirm secret resolves from Key Vault
- Validate token generation
- Remove whitespace / encoding issues
- Restart application

### Prevention
- Never inject Key Vault URI into headers
- Validate headers before sending requests
- Add safe logging for header structure

---

## 6. Forbidden Access (403 – Key Vault)

### Meaning
Access to Key Vault is denied.

### Causes
- Missing RBAC role
- Incorrect role scope
- Access policy conflicts

### Resolution
- Assign correct RBAC role at Key Vault scope
- Remove conflicting policies
- Wait for propagation
- Restart application

---

## 7. Error Classification Matrix

| Layer       | Error Type            | Example |
|------------|----------------------|--------|
| Key Vault  | Identity / RBAC      | InvalidAuthorization |
| Redis      | Authentication       | NOAUTH / WRONGPASS |
| Redis      | Client Parser        | parseError |
| APIM       | Header / Auth Format | InvalidAuthorization |
| Key Vault  | Access Control       | 403 Forbidden |

---

## 8. Quick Diagnostic Checklist

Before escalating, confirm:

- Managed Identity is enabled
- RBAC role assigned correctly
- Key Vault reference syntax is valid
- Secrets exist in Key Vault
- Application restarted after changes
- No Key Vault network restrictions

---

## 9. Troubleshooting Flow (Recommended Order)

1. Identity → Managed Identity enabled
2. Access → RBAC roles assigned
3. Configuration → Key Vault reference format
4. Runtime → Secret resolution and usage

---

## 10. Cross References

- Architecture → `architecture/key-vault.md`
- POC → `poc/key-vault-poc.md`
- Runbook → `runbook/key-vault-migration.md`
- Checklist → `checklist/deployment-checklist.md`

---

## 11. Conclusion

Most issues originate from:

- Identity misconfiguration  
- RBAC permission gaps  
- Incorrect secret resolution  

Following a structured diagnostic approach ensures fast and consistent resolution.