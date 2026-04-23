# 📋 Azure Key Vault Deployment Checklist

## 1. Purpose

Ensure all deployments using Azure Key Vault are secure, validated, and production-ready.

---

## 2. Scope

Applies to Dev, Non-Prod, and Production environments.

---

## 3. Source Control Security

- [ ] No `.env` or secret files in repository  
- [ ] `.gitignore` and `.dockerignore` properly configured  
- [ ] No secrets in code, configs, or pipelines  

---

## 4. Key Vault & Identity

- [ ] Key Vault configured per environment  
- [ ] Secrets created and verified  
- [ ] Managed Identity enabled  
- [ ] Least-privilege access assigned (RBAC)  

---

## 5. Application Configuration

- [ ] No environment file dependency  
- [ ] No hardcoded secrets  
- [ ] Secrets resolved at runtime via Key Vault  
- [ ] Configuration is environment-agnostic  

---

## 6. Build & Deployment

- [ ] Build succeeds  
- [ ] CI/CD pipeline completes without errors  
- [ ] Deployment contains no secrets  
- [ ] Application restarts successfully  

---

## 7. Runtime Validation

- [ ] Application starts successfully  
- [ ] Authentication to Key Vault succeeds  
- [ ] Secrets resolve correctly  
- [ ] Database and external integrations work  
- [ ] No auth or runtime errors  

---

## 8. Monitoring & Logging

- [ ] Monitoring enabled  
- [ ] Key Vault access logs available  
- [ ] No secret leakage in logs  
- [ ] No repeated access failures  

---

## 9. Security Controls

- [ ] RBAC correctly enforced  
- [ ] Least privilege applied  
- [ ] No secrets stored outside Key Vault references  
- [ ] Zero Trust principles maintained  

---

## 10. Rollback Readiness

- [ ] Previous configuration securely stored  
- [ ] Rollback steps documented  
- [ ] Recovery path validated  
- [ ] System can safely revert to last stable state  

---

## 11. Deployment Approval

Deployment is only approved if:

- All checklist items are completed  
- No critical risks remain  
- Runbook has been followed  
- Governance requirements are satisfied  