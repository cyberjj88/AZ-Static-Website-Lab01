# ☁️ Lab 01 — Hosting a Static Website on Azure Blob Storage

## 📋 Overview

In this lab, I deployed a public-facing static website using **Azure Blob Storage** — no web servers, no virtual machines, no complex infrastructure. This is a foundational cloud exercise that introduces key concepts like Platform as a Service (PaaS), serverless hosting, and Azure's resource hierarchy.

Instead of provisioning a full VM or App Service to serve a simple HTML page, Azure lets you flip on **Static Website Hosting** directly on a Storage Account. The result is a globally accessible URL at a fraction of the cost.

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        PUBLIC INTERNET                          │
│                                                                 │
│                         👤 End User                            │
│                             │                                   │
│                  HTTPS Request (Port 443)                       │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      AZURE (East US Region)                     │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │            Resource Group: rg-lab01-[yourname]          │  │
│   │                                                         │  │
│   │   ┌─────────────────────────────────────────────────┐   │  │
│   │   │       Storage Account: stlab01[yourname]        │   │  │
│   │   │         (Standard LRS · Static Website ON)      │   │  │
│   │   │                                                 │   │  │
│   │   │   Primary Endpoint:                             │   │  │
│   │   │   https://stlab01[name].z13.web.core.windows.net│   │  │
│   │   │                                                 │   │  │
│   │   │        ┌──────────────────────────┐            │   │  │
│   │   │        │   Container: $web        │            │   │  │
│   │   │        │  ┌────────────────────┐  │            │   │  │
│   │   │        │  │   index.html  ✅   │  │            │   │  │
│   │   │        │  │   404.html         │  │            │   │  │
│   │   │        │  └────────────────────┘  │            │   │  │
│   │   │        │   (Blob / Public Read)   │            │   │  │
│   │   │        └──────────────────────────┘            │   │  │
│   │   └─────────────────────────────────────────────────┘   │  │
│   └─────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘

  DATA FLOW:
  User Request → Azure CDN Edge → Storage Account Endpoint
                → $web Container → index.html → Response (200 OK)
```

> **Key Insight:** There is no compute layer (no VM, no App Service). Azure's infrastructure serves the blob directly over HTTPS. This is what makes it "serverless."

---

## 🎯 Objectives

By completing this lab, you will be able to:

- [x] Create and manage an **Azure Resource Group**
- [x] Provision an **Azure Storage Account** with correct naming conventions
- [x] Enable **Static Website Hosting** on a Storage Account
- [x] Upload HTML content to the `$web` container
- [x] Access and validate a **live public URL** served from Blob Storage
- [x] **Clean up resources** to avoid unnecessary Azure charges

---

## ✅ Prerequisites

Before starting, confirm you have the following:

- [ ] Active Azure Subscription *(Free Tier is sufficient)*
- [ ] Completed Week 1 Video Modules
- [ ] A basic text editor *(Notepad, TextEdit, or VS Code)*
- [ ] Access to the [Azure Portal](https://portal.azure.com)

---

## 🏷️ Lab Variables & Naming Conventions

Consistent naming makes your resources easy to find, filter, and clean up.

| Field | Value |
|---|---|
| **Resource Group** | `rg-lab01-[yourname]` |
| **Location** | East US |
| **Storage Account** | `stlab01[yourname]` |

> ⚠️ **Storage Account Naming Rules:**
> - All **lowercase** letters and numbers only
> - **No** hyphens, spaces, or special characters
> - Must be **globally unique** across all of Azure
> - Between **3–24 characters**

---

## 🔬 Lab Instructions

### Phase 1 — Create the Resource Group

1. Log in to the [Azure Portal](https://portal.azure.com).
2. In the top search bar, search for **Resource Groups** and select it.
3. Click **+ Create**.
4. Fill in the **Basics** tab:
   - **Subscription:** Select your subscription
   - **Resource Group:** `rg-lab01-[yourname]`
   - **Region:** `(US) East US`
5. Click **Review + create** → **Create**.

---

### Phase 2 — Create the Storage Account

1. Search for **Storage accounts** in the search bar and select it.
2. Click **+ Create**.
3. Fill in the **Basics** tab:
   - **Resource Group:** `rg-lab01-[yourname]`
   - **Storage account name:** `stlab01[yourname]` *(e.g., `stlab01jsmith`)*
   - **Region:** `(US) East US`
   - **Performance:** Standard
   - **Redundancy:** Locally-redundant storage (LRS)
4. Click **Review + create** → wait for validation → **Create**.
5. Once deployed (~30 seconds), click **Go to resource**.

---

### Phase 3 — Enable Static Website Hosting

1. In the left-hand menu of your Storage Account, scroll to **Data management**.
2. Click **Static website**.
3. Toggle the switch from **Disabled** → **Enabled**.
4. Set the following:
   - **Index document name:** `index.html`
   - **Error document path:** `404.html` *(optional but recommended)*
5. Click **Save**.

> 🔑 **CRITICAL:** After saving, a **Primary endpoint URL** will appear (e.g., `https://stlab01jsmith.z13.web.core.windows.net/`). **Copy this URL.** You'll need it for validation.

---

### Phase 4 — Create the HTML File

Open your text editor and paste the following HTML:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My First Cloud Site</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin-top: 50px; background-color: #f0f0f0; }
        h1 { color: #0078d4; }
    </style>
</head>
<body>
    <h1>Hello from the Cloud!</h1>
    <p>This site is hosted on Azure Blob Storage.</p>
    <p>Deployed by: [Your Name]</p>
</body>
</html>
```

Save the file to your Desktop as: **`index.html`**

> ⚠️ Filename is **case-sensitive**. It must be exactly `index.html` — not `Index.html`, not `index.txt`.

---

### Phase 5 — Upload Content to Azure

1. Return to the Azure Portal on your Storage Account.
2. In the left menu, click **Containers** (under *Data storage*).
3. Open the **`$web`** container *(auto-created when you enabled Static Website)*.
4. Click **Upload** at the top.
5. Browse and select your `index.html` file.
6. Click **Upload**.

---

### Phase 6 — Validate Your Deployment ✅

1. Open a new browser tab.
2. Paste the **Primary endpoint URL** you copied in Phase 3.
3. Press Enter.

**Expected Result:**

```
Hello from the Cloud!
This site is hosted on Azure Blob Storage.
Deployed by: [Your Name]
```

🎉 **Congratulations — your serverless website is live!**

---

## 🔧 Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `404 - The requested content does not exist` | Wrong filename | Ensure the file is named exactly `index.html` (case-sensitive) |
| `404 - The requested content does not exist` | Wrong container | Confirm you uploaded to the `$web` container, not a different one |
| `Storage account name already taken` | Name collision across Azure | Append random numbers to your name (e.g., `stlab01jsmith99`) |
| Page loads but shows blank/broken layout | HTML syntax error | Re-copy the HTML from Phase 4 exactly and re-upload |
| Primary endpoint URL not appearing | Static website not saved | Re-open Static website blade and confirm toggle shows **Enabled** |

---

## 🧹 Clean Up Resources

> 💡 **Best Practice:** Always delete lab resources when finished. Orphaned resources accumulate cost and clutter your environment.

1. Navigate to **Resource Groups** in the Azure Portal.
2. Click on `rg-lab01-[yourname]`.
3. Click **Delete resource group**.
4. Type the resource group name to confirm.
5. Click **Delete**.

Deleting the Resource Group removes **all resources inside it** — the Storage Account and all blobs — in one action.

---

## 💡 Key Concepts Covered

| Concept | Definition |
|---|---|
| **PaaS** | Platform as a Service — Microsoft manages the underlying infrastructure; you manage the content and configuration |
| **Serverless Hosting** | Serving content without provisioning or managing a web server |
| **Azure Blob Storage** | Microsoft's object storage solution for unstructured data (files, images, HTML, etc.) |
| **$web Container** | A special, auto-generated blob container that maps to the Static Website endpoint |
| **LRS (Locally Redundant Storage)** | Replicates data 3 times within a single Azure datacenter — lowest cost redundancy tier |
| **Resource Group** | A logical container in Azure that holds related resources for a project or workload |

---

## 📁 Repository Structure

```
lab-01-azure-static-website/
│
├── README.md          # This file — full lab documentation
└── index.html         # Static website source file
```

---

## 🔗 References

- [Azure Blob Storage Documentation](https://learn.microsoft.com/en-us/azure/storage/blobs/)
- [Static Website Hosting in Azure Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blob-static-website)
- [Azure Storage Account Naming Rules](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-overview#storage-account-name)
- [Azure Free Account](https://azure.microsoft.com/en-us/free/)

---
