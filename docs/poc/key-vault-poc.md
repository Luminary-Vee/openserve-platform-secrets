# 🧪 Azure Key Vault Integration POC

## 1. Objective

Validate that Azure App Service can securely retrieve secrets from Azure Key Vault using **Managed Identity**, eliminating the need for `.env` files and embedded credentials.

---

## 2. Scope

This Proof of Concept validates the following capabilities:

- Secure storage of secrets in Azure Key Vault  
- App Service integration using Key Vault references  
- Managed Identity authentication  
- Runtime secret resolution at application startup  
- RBAC-based access control enforcement  

### Out of Scope

- Secret rotation automation  
- Multi-region redundancy  
- CI/CD pipeline integration  

---

## 3. Architecture Overview

Application → Managed Identity → Azure Key Vault → Secure Secret Retrieval → Runtime Injection

---

## 4. Implementation Summary

### 4.1 Key Vault Configuration

Azure Key Vault instance created and configured with required secrets:

- DATABASE_PASSWORD *(database authentication)*  
- AZURE_CLIENT_SECRET *(service authentication)*  
- API_GATEWAY_SUBSCRIPTION_KEY *(external API access)*  

---

### 4.2 App Service Configuration

- System Assigned Managed Identity enabled  
- Role assigned: **Key Vault Secrets User**  

Key Vault reference used in App Settings:

@Microsoft.KeyVault(SecretUri=https://<vault-name>.vault.azure.net/secrets/<secret-name>/)

---

## 5. Key Behaviour

- Secrets are resolved at runtime by Azure App Service  
- No `.env` files are used in deployment  
- No hardcoded credentials exist in codebase  
- Secrets are never persisted locally or in logs  

---

## 6. Validation Results

- Application starts successfully  
- Managed Identity authentication succeeds  
- Secrets resolve correctly from Key Vault  
- Database connection established  
- External API authentication successful  
- No fallback to environment variables detected  

---

## 7. Conclusion

This Proof of Concept confirms that Azure Key Vault with Managed Identity provides a secure, production-ready replacement for traditional `.env`-based secret management.