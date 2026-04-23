# 🚀 Azure Key Vault Migration Runbook

## 1. Purpose

This runbook defines the standardized process for migrating application secrets from:
- `.env` files
- App Service Configuration
- Embedded credentials

to **Azure Key Vault using Managed Identity and RBAC**.

---

## 2. Scope

All environments:

- Development
- Non-Prod
- Production

---

## 3. Prerequisites

Before starting migration:

- Azure Key Vault created per environment
- Required secrets already stored in Key Vault:
  - DATABASE-PASSWORD
  - CLIENT-SECRET
  - API-SUBSCRIPTION-KEY
- App Service configured with System Assigned Managed Identity
- RBAC role assigned: **Key Vault Secrets User**
- Application deployment access available

---

## 4. Pre-Migration Validation (IMPORTANT)

###  Identity Check
- Confirm Managed Identity is enabled on App Service
- Confirm identity exists in Azure AD

---

### Key Vault Access Check
- Validate App Service has **Key Vault Secrets User** role
- Confirm Key Vault is reachable from App Service

---

### Secret Availability
- Ensure all required secrets exist in Key Vault
- Confirm secret names match application expectations (case-sensitive)

---

### Source Control Security Check

Ensure repository does NOT contain exposed secrets:

#### Verify `.gitignore`
Must include:
- `.env`
- `.env.*`
- `*.env`

#### Verify `.dockerignore`
Must include:
- `.env`
- `.env.*`

#### Rule
- No secrets must exist in source control or Docker build context

---

## 5. Migration Steps

### Step 1: Create or Verify Secrets in Key Vault
Ensure all required secrets exist in Azure Key Vault.

---

### Step 2: Enable Managed Identity
In Azure Portal:
- Navigate to App Service
- Go to Identity
- Enable System Assigned Identity
- Save changes

---

### Step 3: Assign RBAC Permissions
Assign role:

- **Key Vault Secrets User**

Scope:
- Target Key Vault only

---

### Step 4: Update Application Configuration

Replace App Settings values with Key Vault references:

@Microsoft.KeyVault(SecretUri=https://<vault-name>.vault.azure.net/secrets/<secret-name>/)
