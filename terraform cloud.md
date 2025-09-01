Perfect 👍 thanks for sharing all your Terraform Cloud notes.
You’ve already got **detailed content**, so I’ll **keep everything intact** but make it **concise, structured, and interview-ready**. I’ll also add a few **extra important points** that are often asked in real-time/ interviews.

---

# 🌐 Terraform Cloud – Complete Cheat Sheet (Concise + Complete)

---

## ✅ **Core Features**

* Manages **state files** securely (remote backend)
* History of **runs** & **states**
* Easy **variable injection** (sensitive vars encrypted)
* **Tagging** resources & runs
* **Run Triggers** (chaining workspaces)
* Use any Terraform version per workspace
* **Global state sharing**
* Commenting on Runs
* Notifications (Webhooks, Email, Slack)
* **Permissions** (Org & Workspace level)
* **Policy as Code** (Sentinel)
* **MFA + SSO** (Business tier)
* Cost Estimation (Teams+ Governance tier)
* Integrations: ServiceNow, Splunk, K8s, custom tasks

---

## 🔑 **Key Concepts**

### 1. **Organization**

* Top-level container for **workspaces, teams, users**
* Example: `acme-corp`

### 2. **Workspace**

* Maps to **one Terraform project/environment**
* Stores config, variables, state, run history
* Example: `web-app-dev`, `web-app-prod`

### 3. **Run**

* Execution event (Plan + Apply)
* Triggered via **CLI, VCS, or API**

### 4. **Team**

* Group of users with **shared permissions**
* Can have **Read, Plan, Apply, Admin** roles
* Example: DevOps team (full), QA team (read-only)

📌 **Hierarchy:**

```
Organization
├── Teams (Users + Permissions)
└── Workspaces (Run history, Variables, State)
```

---

## 🔁 **Terraform Cloud Workflows**

| Workflow       | Trigger     | Best For          | Notes                           |
| -------------- | ----------- | ----------------- | ------------------------------- |
| **CLI-driven** | Local CLI   | Devs/test         | Uses Cloud as backend           |
| **VCS-driven** | Git commits | IaC teams         | Auto plan/apply on PRs          |
| **API-driven** | REST API    | CI/CD, automation | Used by Jenkins, GitHub Actions |

---

## 🏢 **Organization-Level Permissions**

* Manage policies (Sentinel)
* Manage policy overrides
* Manage VCS settings
* Manage all workspaces

👑 **Org Owners Only**:

* Publish private modules
* Invite users, manage teams
* View **secret variables**
* Control **billing**
* Delete entire org

---

## 🧱 **Workspace-Level Permissions**

### Custom (granular):

* Read runs, plan, apply
* Lock/unlock workspace
* Read/write variables
* Read/write state versions

### Fixed Roles:

* **Read** → view runs, vars, state
* **Plan** → queue plans, view vars/state
* **Write** → apply, lock, edit vars/state

👑 **Workspace Admin** → full control of settings + delete workspace

---

## 🔐 **API Tokens**

1. **Organization Token**

   * Full org-wide access
   * Only **one active** at a time
   * Only org owners can create
   * Use for **setup/config** (not daily ops)

2. **Team Token**

   * Access to team’s workspaces
   * One per team
   * Created by any team member
   * Good for **shared automation**

3. **User Token**

   * Scope = user’s permissions
   * Flexible: CLI, API, automation
   * Supports machine users

📌 **Summary:**

* User = personal/CLI
* Team = group automation
* Org = bootstrap/setup

---

## ⚡ Extra Interview Tips

* **State Locking:** Terraform Cloud automatically locks state during runs (avoids `force-unlock` issues common in S3 + DynamoDB).
* **Policy as Code (Sentinel):** Used for compliance (e.g., enforce tags, block big instances).
* **Variable sets:** Share common vars across multiple workspaces (e.g., AWS creds).
* **Run Tasks:** Integrate with security tools (e.g., Checkov, Prisma) before apply.
* **Terraform Enterprise** = Self-hosted version of Terraform Cloud (for private data centers).

---

