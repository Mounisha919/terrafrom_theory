Got it ✅ — you want everything **preserved**, but made **concise and structured**, with only missing but important context added. Here’s the cleaned-up version in Markdown:

---

# Terraform State

## What is State?

State = the condition of cloud infrastructure at a specific point in time.

Example: A VM running CentOS on AWS with type `t2.micro`.

Terraform stores this in `terraform.tfstate` (JSON file).

* Maintains a **mapping** between Terraform resources and real cloud resources.

---

## Example: `terraform.tfstate` (simplified)

```json
{
  "version": 4,
  "terraform_version": "1.0.4",
  "resources": [
    {
      "type": "aws_instance",
      "name": "example_server",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "attributes": {
            "ami": "ami-0c02b2c1",
            "instance_type": "t2.micro"
          }
        }
      ]
    }
  ]
}
```

---

## Terraform Workflow

1. Write configuration files:
   `main.tf`, `variables.tf`, `terraform.tfvars`
2. Run `terraform apply` → Terraform will:

   * Connect to cloud provider
   * Create infrastructure
   * Save current state to `terraform.tfstate`

**State file** ensures Terraform knows which resources it manages.

---

# Terraform State Commands

Terraform provides `terraform state` subcommands to **inspect, modify, or repair** state files.
Mostly for **advanced scenarios** (fixing drift, refactoring, migration).

---

### 1. `terraform state list`

* **Purpose**: Lists all resources tracked in the current state file.
* **Example**:

  ```bash
  terraform state list
  ```

  **Output**:

  ```
  aws_instance.example
  aws_s3_bucket.my_bucket
  ```

---

### 2. `terraform state show`

* **Purpose**: Show detailed attributes of a resource from state.
* **Example**:

  ```bash
  terraform state show aws_instance.example
  ```

---

### 3. `terraform state mv`

* **Purpose**: Move/rename a resource in state.

  * Rename resource
  * Move resource into/out of modules
* **Examples**:

  ```bash
  terraform state mv aws_instance.old aws_instance.new
  terraform state mv packet_device.worker module.worker.packet_device.worker
  ```
* **Important**:

  * Only updates **state**, not real infra.
  * Prevents Terraform from destroying/recreating when you rename/move blocks in `.tf` code.

---

### 4. `terraform state rm`

* **Purpose**: Remove a resource from state.
* **Example**:

  ```bash
  terraform state rm aws_instance.example
  ```
* Effect: Resource exists in cloud but **Terraform no longer tracks it**. Use carefully.

---

### 5. `terraform state pull`

* **Purpose**: Download/print current state (useful for remote backends).
* **Example**:

  ```bash
  terraform state pull
  ```

---

### 6. `terraform state push`

* **Purpose**: Manually upload a local state to remote backend.
* **Example**:

  ```bash
  terraform state push terraform.tfstate
  ```

---

### 7. `terraform state replace-provider`

* **Purpose**: Replace provider references in state (during provider migrations).
* **Example**:

  ```bash
  terraform state replace-provider old new
  ```

---

# Terraform State Backups

* Any **modifying command** writes a backup file (`terraform.tfstate.backup`).
* **Read-only commands** (`list`, `show`) don’t modify state.
* Backups **cannot be disabled** (safety).
* To delete:

  ```bash
  rm terraform.tfstate.backup
  ```

---

# When to Use State Commands

* After **renaming/moving resources** in `.tf` files (to avoid recreation).
* When you **manually created/deleted/moved** resources outside Terraform.
* For **state repair** or migration.
* For **debugging drift** without applying.

---

## Key Notes

* State commands **do not change real infrastructure**, only how Terraform tracks it.
* Real infra changes (like EC2 instance name) → update `.tf` code (`tags.Name = "new"`) and apply.
* Always version-control your state if possible (remote backends like S3, GCS, Terraform Cloud).

---
Perfect 👍 You want your **Terraform State Backups** section rewritten in a **clear, structured way** with your original source lines preserved. Here’s the final version including everything you asked for:

---

# Terraform State Backups

### 📌 Source line (preserved)

> **All terraform state subcommands that modify state will write a backup file. Read-only commands will not modify state (e.g. list, show).**

---

## 🔹 What does this mean?

Whenever you run a Terraform command that **modifies the state file**, Terraform **automatically creates a backup** of the current state file.
This protects you from accidental **loss** or **corruption** of state.

---

## 🔹 How does it work?

1. Terraform takes the current state stored in `terraform.tfstate`.
2. It creates a backup copy called `terraform.tfstate.backup`.

So, in your working directory you will see:

```
terraform.tfstate
terraform.tfstate.backup
```

---

## 🔹 Why does Terraform create backups?

* To enforce **best practices for disaster recovery**.
* If `terraform.tfstate` gets corrupted or overwritten, you can restore from the backup (`.backup`).

This ensures you don’t lose track of your infrastructure.

---

## 🔹 Can you disable this behavior?

📌 **Source line (preserved):**

> **Backups cannot be disabled. This is by design to enforce best-practice for recovery.**

✅ So **No** — Terraform does not allow disabling backups.
It is a **built-in safety feature**.

---

## 🔹 How to remove the backup file?

📌 **Source line (preserved):**

> **To get rid of the backup file you need to manually delete the files**

If you don’t need the backup anymore:

```bash
rm terraform.tfstate.backup
```

⚠️ **Note:** Delete only if you are sure you don’t need recovery options.

---

