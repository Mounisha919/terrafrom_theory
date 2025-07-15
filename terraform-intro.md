
## 🌍 What is Terraform?

**Terraform** is an **open-source**, **cloud-agnostic** tool for managing infrastructure as code (**IaC**).

* **Open-source** → Free and customizable.
* **Cloud-agnostic** → Works across AWS, Azure, GCP, etc.

---

## 🧠 How Terraform Works

Terraform uses **declarative** `.tf` files written in **HCL (HashiCorp Configuration Language)**:

> "You declare *what* you want, Terraform figures out *how* to get it."


---

## 🔄 Terraform Lifecycle (Step-by-Step)

| Step     | Command              | Purpose                  |
| -------- | -------------------- | ------------------------ |
| Code     | –                    | Write config in `.tf`    |
| Init     | `terraform init`     | Initialize project       |
| Validate | `terraform validate` | Check config correctness |
| Plan     | `terraform plan`     | Preview changes          |
| Apply    | `terraform apply`    | Create/update infra      |
| Destroy  | `terraform destroy`  | Delete infra             |

---

## ⚙️ Change Automation

Terraform automates infrastructure changes. You get predictable, consistent deployments.

| Concept        | Meaning                               | Command Example               |
| -------------- | ------------------------------------- | ----------------------------- |
| Execution Plan | Preview of what Terraform will do     | `terraform plan`              |
| Resource Graph | Visual map of dependencies            | `terraform graph`             |
| ChangeSet      | History of changes (like Git commits) | `terraform show` or `git log` |

---

## 📊 Visualizing Execution Plans

Use this to create visual diagrams of dependencies:

```bash
terraform graph | dot -Tsvg > graph.svg
```

* Requires **GraphViz** installed.
* Shows **which resources depend on others** and execution order.

---

## 🧩 Terraform Core vs Plugins

| Component      | Role                                                     | Example                    | Analogy       |
| -------------- | -------------------------------------------------------- | -------------------------- | ------------- |
| Terraform Core | Main engine – parses config, creates plan, calls plugins | Built-in by HashiCorp      | Brain         |
| Plugins        | Actual executors – talk to AWS, GCP, etc.                | AWS, Azure, Docker plugins | Workers       |
| RPC            | Protocol Core uses to talk to plugins                    | Remote Procedure Call      | Communication |
| Written in     | Go language (both core and plugins)                      | Fast and compiled          | Language      |

---

### 🔌 Types of Plugins

Terraform plugins extend its functionality. There are four main types:

1. **Provider Plugins**

   * Interact with APIs of cloud providers and services.
   * Examples: `aws`, `google`, `azurerm`, `kubernetes`, `mysql`, `datadog`

2. **Provisioner Plugins**

   * Used to execute scripts on a resource after it's created.
   * Examples: `remote-exec`, `local-exec`, `file`

3. **Backend Plugins**

   * Define **where Terraform stores state** (local, S3, Azure Blob, GCS, etc.)
   * Not declared like other plugins; configured in the backend block.

4. **Authenticator Plugins**

   * Handle secure credential exchanges for providers.
   * Example: AWS SSO, Azure CLI login, Vault.

---

### 🧠 Interview Tip

> "Terraform Core is the brain — it reads your `.tf` code and sends instructions to plugins, which do the actual work like creating VMs or networks."

> "Plugins include providers (AWS, Azure), provisioners (scripts), backends (S3), and authenticators."

---

## ✅ Terraform Use Cases

| Use Case                | Simple Meaning                                         |
| ----------------------- | ------------------------------------------------------ |
| Exotic Providers        | Works with less common APIs (e.g., Heroku, GitHub)     |
| Multi-Tier Apps         | Split into frontend/backend/database modules           |
| Disposable Environments | Quickly create & destroy demo/test infra               |
| Resource Schedulers     | Schedule jobs or manage Spark, Docker, Hadoop clusters |
| Multi-Cloud Deployment  | Manage AWS + GCP + Azure from one script               |

---

