# End-to-End CI/CD Pipeline — PHP App on Azure Linux Web App with Custom Domain

## Real-World Scenario

> You just got hired as a Cloud Engineer at a startup called **Your Techie Hub**. The company already has a PHP website but struggles with slow, manual updates and an unprofessional default URL. They want a reliable, automated deployment system without managing servers to keep costs low.

**Requirements:**
- Automatic deployment on every code update
- A custom domain (e.g. `www.mustydevops.com.ng`) instead of the default Azure URL
- HTTPS security
- A scalable, serverless-friendly setup (no server management)

---

## Project Objective

Build a CI/CD pipeline that:
- Deploys a PHP web app to **Azure Linux App Service**
- Auto-deploys on every `git push` using **GitHub Actions**
- Secures credentials using **GitHub Secrets**
- Connects a **custom domain** with **managed SSL (HTTPS)**

---

## Architecture

```
Developer (VS Code)
       │
       │  git push
       ▼
  GitHub Repository
  (Musty2025x/elearning-php)
       │
       │  Triggers on push to main
       ▼
  GitHub Actions Workflow
  (.github/workflows/main_elearning-app.yml)
       │
       │  OIDC login → azure/login@v2
       │  Deploy   → azure/webapps-deploy@v3
       ▼
  Azure Linux Web App
  (elearning-app — PHP 8.4, West US 3, Basic B1)
       │
       ▼
  Custom Domain + Managed SSL
  https://www.mustydevops.com.ng
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Source Control | GitHub |
| CI/CD | GitHub Actions (OIDC, secretless auth) |
| Hosting | Azure App Service — Linux, PHP 8.4 |
| Pricing Plan | Basic B1 (required for custom domain + SSL) |
| Domain Registrar | Qservers (or Namecheap / GoDaddy) |
| SSL | Azure Managed Certificate (free, auto-renewed) |

---

## Prerequisites

- Azure account with an active subscription
- GitHub account
- A registered custom domain (e.g. `www.mustydevops.com.ng`)
- Git and VS Code installed locally
- PHP app source files ([download here](https://drive.google.com/drive/folders/13xUDk_dq-fkQ6XBOAVmXAeEPzjJW14UI?usp=sharing))

---

## Step 1 — Prepare Your PHP Application

Download and unzip the PHP app, then open the folder in VS Code.

**App structure:**
```
elearning-php/
├── index.php
├── composer.json
├── .github/
│   └── workflows/
│       └── main_elearning-app.yml
├── css/
├── js/
└── images/
```

---

## Step 2 — Push Code to GitHub

Open a terminal in the project folder and run:

```bash
git init
git add .
git commit -m "Initial PHP app commit"
git branch -M main
git remote add origin https://github.com/<your-username>/elearning-php
git push -u origin main
```

**Create the GitHub repository first:**
1. Go to [github.com](https://github.com) → click **+** → **New repository**
2. Repository name: `elearning-php`
3. Leave it empty (no README) — your local code will be pushed into it

---

## Step 3 — Create Azure Linux Web App

1. Go to **Azure Portal → App Services → Create**
2. Configure as follows:

| Field | Value |
|---|---|
| Resource Group | `elearning-rg` (create new) |
| Name | `elearning-app` |
| Publish | Code |
| Runtime stack | PHP 8.4 |
| Operating System | Linux |
| Region | West US 3 |
| Pricing Plan | Free F1 *(upgrade to B1 later for custom domain)* |

3. Click **Review + Create → Create**
4. Click **Go to resource**

Your default domain will be assigned automatically:
```
https://elearning-app-b2hgevaughhzh2ec.westus3-01.azurewebsites.net
```

## Sreenshot of Azure Web App Overview

![alt text](<asset/Screenshot 2026-05-05 102328.png>)
![alt text](<asset/Screenshot 2026-05-05 102346.png>)
![alt text](<asset/Screenshot 2026-05-05 102653.png>)
---

## Step 4 — Enable CI/CD via GitHub Actions (Azure Deployment Center)

Azure's built-in Deployment Center handles all the wiring automatically:

1. In your Web App, click **Deployment Center**
2. Configure:

| Field | Value |
|---|---|
| Source | GitHub |
| Repository | `elearning-php` |
| Branch | `main` |

3. Click **Save**

## screenshot of Azure Deployment Center Configuration

![alt text](<asset/Screenshot 2026-05-05 103111.png>)
![alt text](<asset/Screenshot 2026-05-05 103129.png>)

**What Azure does automatically:**
- Connects to your GitHub repository
- Creates the workflow file `.github/workflows/main_elearning-app.yml`
- Creates GitHub Secrets for OIDC authentication:
  - `AZUREAPPSERVICE_CLIENTID_...`
  - `AZUREAPPSERVICE_TENANTID_...`
  - `AZUREAPPSERVICE_SUBSCRIPTIONID_...`
- Enables auto-deploy on every push to `main`

> **Tip:** Click **Preview file** in the Deployment Center to review the generated workflow before saving.

---

## Step 5 — Review the Generated GitHub Actions Workflow

After saving, refresh your GitHub repository. The workflow file will appear at:

```
.github/workflows/main_elearning-app.yml
```
## screenshot of GitHub Actions Workflow File
![alt text](<asset/Screenshot 2026-05-05 103412.png>)

---

## Step 6 — Monitor Deployment Logs in Azure

1. Go to **Azure Portal → App Service → Deployment Center**
2. Click the **Logs** tab
3. Confirm the deployment status shows **Succeeded**

---

## screenshot of Azure Deployment Logs
![alt text](<asset/Screenshot 2026-05-05 110051.png>)

## Step 7 — Verify Deployment on Default Domain

Open the default domain in your browser:

```
https://elearning-app-b2hgevaughhzh2ec.westus3-01.azurewebsites.net/
```

The PHP application should be live and rendering correctly.

## screenshot of Deployed App on Default Domain
![alt text](<asset/Screenshot 2026-05-05 110119.png>)

---

## Step 8 — Test Auto-Deployment (CI/CD in Action)

Make a visible change to your app to verify the pipeline triggers automatically:

1. Open `index.php` in VS Code and make a change (e.g. update a heading on line 83)
2. Pull the latest workflow file first, then push:

```bash
git pull origin main
git add .
git commit -m "Updated index.php - added Your Techie Hub branding"
git push origin main
```
## screenshot of index.php change in VS Code
![alt text](<asset/Screenshot 2026-05-05 111058.png>)


3. Go to **GitHub → Actions** — watch the workflow run in real time
4. Once complete, refresh your default domain URL — the change is live automatically

## screenshot of GitHub Actions Workflow Run
![alt text](<asset/Screenshot 2026-05-05 110825.png>)
![alt text](<asset/Screenshot 2026-05-05 110840.png>)


## screenshot of Updated App on Default Domain
![alt text](<asset/Screenshot 2026-05-05 111648.png>)
---

## Step 9 — Add a Custom Domain

### 9.1 — Upgrade to Basic B1 Plan

> The Free (F1) tier does **not** support custom domains or SSL. You must upgrade first.

1. In your Web App, go to **App Service Plan → Scale up**
2. Select **Basic B1**
3. Click **Select**

## screenshot of Azure App Service Plan Upgrade
![alt text](<asset/Screenshot 2026-05-05 111943.png>)
![alt text](<asset/Screenshot 2026-05-05 112009.png>)

### 9.2 — Add Domain in Azure

1. In your Web App, click **Custom Domains**
2. Click **Add custom domain**
3. Enter: `www.yourdomain.com` (e.g. `www.yourtechiehub.com.ng`)
4. Azure will display two DNS records to create — copy both values

## screenshot of Azure Custom Domain Configuration
![alt text](<asset/Screenshot 2026-05-05 112234.png>)
---

## Step 10 — Configure DNS at Your Domain Registrar

Log in to your domain registrar (Qservers, Namecheap, GoDaddy, etc.) and add the following records:

### CNAME Record

| Field | Value |
|---|---|
| Type | `CNAME` |
| Host / Name | `www` |
| Value | `elearning-app-xxxx.azurewebsites.net` |

### TXT Record (ownership verification)

| Field | Value |
|---|---|
| Type | `TXT` |
| Host / Name | `asuid.www` |
| Value | *(the verification code from Azure — e.g. `577F850E...`)* |

Save both records and wait **5–30 minutes** for DNS propagation.

## screenshot of DNS Records at Registrar
![alt text](<asset/Screenshot 2026-05-05 112515.png>)
![alt text](<asset/Screenshot 2026-05-05 112604.png>)

### Validate in Azure

1. Return to **Azure Portal → Custom Domains**
2. Click **Validate**
3. Click **Add**

Azure verifies the TXT record proves ownership, then binds the custom domain to your Web App.

## screenshot of Azure Custom Domain Validation
![alt text](<asset/Screenshot 2026-05-05 112731.png>)
---

## Step 11 — Enable HTTPS / SSL

1. In **Custom Domains**, click **Add binding** next to your custom domain
2. Select:
   - Certificate type: **Managed certificate (free)**
   - TLS/SSL type: **SNI SSL**
3. Click **Validate → Add**

Once complete, your domain is secured with a free Azure-managed SSL certificate that auto-renews.

## screenshot of Azure SSL Configuration
![alt text](<asset/Screenshot 2026-05-05 112955.png>)
![alt text](<asset/Screenshot 2026-05-05 113128.png>)
---

## Step 12 — Verify Custom Domain with HTTPS

Open your custom domain in the browser:

```
https://www.mustytechiehub.com.ng
```

The site loads over HTTPS with a valid certificate. 🎉

## screenshot of Deployed App on Custom Domain with HTTPS
![alt text](<asset/Screenshot 2026-05-05 113212.png>)
---

## Step 13 — Confirm Full CI/CD Pipeline with Custom Domain

Your complete flow is now:

```
Code change (VS Code)
       │
       ▼  git push origin main
GitHub Repository
       │
       ▼  Triggers workflow
GitHub Actions (build → deploy)
       │
       ▼  azure/webapps-deploy
Azure Linux Web App
       │
       ▼  Live instantly
https://www.mustytechiehub.com.ng
```

Test it end-to-end:

## screenshot of Code Change in VS Code
![alt text](<asset/Screenshot 2026-05-05 145134.png>)

```bash
# Make another change
git add .
git commit -m "add I love to index.php file"
git push origin main
```

## screenshot of GitHub Actions Workflow Run
![alt text](<asset/Screenshot 2026-05-05 114434.png>)

## screenshot of elearning Deployment Logs
![alt text](<asset/Screenshot 2026-05-05 114506.png>)

Watch **GitHub → Actions**, then visit `https://www.mustytechiehub.com.ng` — the change reflects automatically with zero manual intervention.

## screenshot of Updated App on Custom Domain
![alt text](<asset/Screenshot 2026-05-05 114528.png>)

---

## Output

### GitHub Actions — Successful Pipeline Run

```
Run actions/download-artifact@v4
  Downloading single artifact: php-app
  Artifact download completed successfully.
  Total of 1 artifact(s) downloaded ✅

Run azure/login@v2
  Running Azure CLI Login...
  Federated token details:
    issuer    - https://token.actions.githubusercontent.com
    subject   - repo:Musty2025x/elearning-php:ref:refs/heads/main
    audience  - api://AzureADTokenExchange
  Attempting Azure CLI login by using OIDC...
  Azure CLI login succeeded. ✅

Run azure/webapps-deploy@v3
  app-name: elearning-php
  slot-name: Production
  Validating SPN Linux Web App inputs... ✅
  Predeployment Step Started ✅
  Deployment Step Started ✅
  Deployment successful. ✅
```

### GitHub Repository Secrets

| Secret Name | Purpose |
|---|---|
| `AZUREAPPSERVICE_CLIENTID_...` | Service Principal Client ID for OIDC login |
| `AZUREAPPSERVICE_TENANTID_...` | Azure AD Tenant ID |
| `AZUREAPPSERVICE_SUBSCRIPTIONID_...` | Azure Subscription ID |

### DNS Records Configured

| Type | Host | Value | Purpose |
|---|---|---|---|
| `CNAME` | `www` | `elearning-app-xxxx.azurewebsites.net` | Points domain to Azure Web App |
| `TXT` | `asuid.www` | `577F850E...` | Proves domain ownership to Azure |

---

## Errors & Fixes

### ❌ Issue 1 — `Cannot find module 'express'` (local dev)

**Cause:** Running `node server.js` before installing dependencies, or running from the wrong directory.

**Fix:**
```bash
cd ~/nodejs-app-monitoring   # project root where package.json lives
npm install
node app/server.js
```

---

### ❌ Issue 2 — `No subscriptions found` (GitHub Actions OIDC login)

**Cause:** The Service Principal created by Azure has no role assigned on the subscription.

**Fix:** Assign the Contributor role:
```bash
az role assignment create \
  --assignee <client-id> \
  --role Contributor \
  --scope /subscriptions/<subscription-id>
```

---

### ❌ Issue 3 — Unable to Add Custom Domain on Free Plan

**Cause:** Azure Free (F1) tier does not support custom domain binding.

**Fix:** Upgrade App Service Plan to **Basic B1** via **Scale up** in the Azure Portal.

---

### ❌ Issue 4 — Domain Not Resolving After DNS Configuration

**Cause:** DNS records not fully propagated or CNAME value was incorrect.

**Fix:**
- Verify CNAME points to `elearning-app-xxxx.azurewebsites.net` (not the Front Door or storage URL)
- Wait 10–30 minutes for propagation
- Revalidate in **Azure Portal → Custom Domains**

---

### ❌ Issue 5 — "Hostname Not Configured" Error

**Cause:** DNS record added at registrar but domain not registered inside Azure App Service.

**Fix:** Navigate to **Custom Domains → Validate → Add** in the Azure Portal to complete the binding.

---

### ❌ Issue 6 — HTTPS Not Working / Site Not Secure

**Cause:** SSL certificate not created or not bound to the custom domain.

**Fix:** Go to **Custom Domains → Add binding → Managed Certificate → SNI SSL → Add**

---

## Cleanup

To avoid ongoing Azure charges after the lab:

1. Go to **Azure Portal → Resource Groups**
2. Select `elearning-rg`
3. Click **Delete resource group**
4. Confirm by typing the resource group name

This deletes the App Service, App Service Plan, and all associated resources in one action.

## screenshot of Azure Resource Group Deletion
![alt text](<asset/Screenshot 2026-05-05 115556.png>)

---

## Project Summary

| Component | Detail |
|---|---|
| App | PHP elearning website |
| Repository | `github.com/Musty2025x/elearning-php` |
| CI/CD | GitHub Actions — OIDC authentication (secretless) |
| Hosting | Azure Linux Web App — PHP 8.4 — West US 3 |
| Pricing Plan | Basic B1 |
| Default URL | `https://elearning-app-b2hgevaughhzh2ec.westus3-01.azurewebsites.net` |
| Custom Domain | `https://www.mustytechiehub.com.ng` |
| SSL | Azure Managed Certificate (free, auto-renewed) |
| DNS Registrar | Qservers |

---

## References

- [Azure App Service Documentation](https://learn.microsoft.com/en-us/azure/app-service/)
- [GitHub Actions — azure/login](https://github.com/Azure/login)
- [GitHub Actions — azure/webapps-deploy](https://github.com/Azure/webapps-deploy)
- [Azure Custom Domain Configuration](https://learn.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain)
- [Azure Managed Certificates](https://learn.microsoft.com/en-us/azure/app-service/configure-ssl-certificate)