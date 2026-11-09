# 🏦 Salesforce Bank Lockdown: Shifting Security from 69% to 96% for FCA & GDPR Compliance

**Project Sandbox:** Northbridge Bank plc Simulation  
**Role:** Lead Salesforce Security Architect & Consultant  
**Core Compliance Frameworks:** UK GDPR (Data Minimisation), FCA Consumer Duty, PCI DSS v4.0  

---

## 📈 Executive Impact Summary
*   **Security Health Check Score:** Engineered an infrastructure configuration shift from a **69% (Poor)** vulnerability baseline to a **96% (Excellent)** enterprise-hardened state.
*   **Privilege Reduction:** Mitigated critical risk by deprecating bloated, cloned admin profiles and removing *Modify All Data* permissions down to **0 users**.
*   **Data Segregation:** Restructured data access from an open Public Read/Write layout to a strict **Private OWD** architecture, restoring secure persona access via custom Permission Set Groups.

---

## 🚨 The Business Challenge
An internal audit flagged Northbridge Bank’s core Salesforce configuration as high-risk ahead of a critical regulatory review. The org exposed sensitive retail banking data, commercial loans, and personal PII (National Insurance numbers, bank account details, customer vulnerability flags) to all 420 staff members globally. Marketing interns could modify active underwriting applications, and a lack of audit visibility compromised accountability during shift rotations.

## 🛠️ Implemented Architecture & Solution Layers
1. **Identity & Perimeter Defense:** Enforced org-wide Multi-Factor Authentication (MFA) via Salesforce Authenticator, established an enterprise 12-character complex password policy, and implemented profile-level Login IP Ranges to lock down network access.
2. **Granular Access Control:** Applied Salesforce best practices by provisioning native **Minimum-Access Profiles** as a clean baseline, bundling functional object and field permissions exclusively into modular, role-based **Permission Set Groups** (Adviser, Underwriter, Agent, KYC Analyst).
3. **Data Segregation & Masking:** Flipped OWDs to Private, implemented criteria-based sharing rules for regional isolation, deployed a platform **Restriction Rule** to hide internal staff financial accounts from front-line agents, and built a Record-Triggered Flow to auto-mask account numbers (`****4321`).
4. **Compliance Muting & Auditing:** Engineered a custom **Muting Permission Set** to securely block the Internal Audit persona from viewing raw customer PII while retaining global "View All" record validation. Activated **Field History Tracking** across 15 high-risk financial fields to establish a permanent regulatory trail.

---

## 👥 Multi-Persona Security Matrix
This table details the strict Principle of Least Privilege (PoLP) enforced across the bank's operational units:

| Persona Role | Account Rows | Financial Account Rows | Case Object (Complaints) | National Insurance No. | Account Number Masking |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Retail Adviser (NW)** | Owned rows only | Owned rows only | Read / Edit (Owned) | 🚫 Completely Hidden | 🟢 Full Access (`12345678`) |
| **Retail Adviser (SE)** | Owned rows only | Owned rows only | Read / Edit (Owned) | 🚫 Completely Hidden | 🟢 Full Access (`12345678`) |
| **Underwriter** | Read Only (Via SR) | Read / Edit (Via SR) | 🚫 Blocked | 🚫 Completely Hidden | 🟢 Full Access (`12345678`) |
| **Contact Centre Agent**| Standard only (Via SR) | Standard only (Via SR) | Full Queue Pipeline | 🚫 Completely Hidden | 🟡 Masked Last 4 (`****5678`) |
| **KYC Analyst** | Pending Review Only | Pending Review Only | 🚫 Blocked | 🟢 Full Decrypted Read | 🟢 Full Access (`12345678`) |
| **Internal Audit** | 👁️ Global View All | 👁️ Global View All | 🚫 Blocked | 🚫 Muted / Stripped | 🚫 Muted / Stripped |

*Legend: SR = Criteria/Owner Sharing Rule Override; Masked Last 4 = Processed via Record-Triggered Automation Flow.*

---


### 🔒 Visual Verification: Front-Line Agent Data Masking (Carl Agent View)
<img src="evidence-screenshots/data-masking-carl.png" width="800" alt="Salesforce Account Number Masking In Action" />

*UAT verification showing the system state when logged in as Carl Agent. The native record-triggered flow successfully restricts full account exposures and masks the data value to `****5678` to satisfy strict PCI DSS requirements.*


## 🧪 UAT Testing Log Summary
To validate the architecture against the security matrix, dynamic user rotation testing was executed using the platform's native "Login As" feature:
*   **TC-01 (Passed):** Verified Ava Adviser (North West) cannot view or search Harry Patel (South East) due to Private OWD restrictions.
*   **TC-02 (Passed):** Confirmed Ava Adviser can view her own record (Amelia Hart) with full access to edit Income, while the National Insurance Number remains completely hidden via FLS.
*   **TC-04 (Passed):** Verified Carl Agent (Contact Centre) sees Amelia Hart's record as entirely Read-Only, with the Financial Account number securely masked to `****4321`.
*   **TC-12 (Passed):** Proven that when Kemi Analyst rotates into the Internal Audit persona, she retains global "View All" data visibility, but the Muting Permission Set successfully zeroes out and strips away the sensitive National Insurance Number and Annual Income fields on-the-fly.
