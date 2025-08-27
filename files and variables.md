

## ⚙️ Terraform Settings Block – Easy Explanation

✅ **What is the `terraform` Block?**
The `terraform` block is used to configure Terraform's **global settings**, like:

* Required providers
* Required Terraform version
* Backend configuration
* Cloud/Enterprise settings

🧠 It’s like Terraform’s **control center** for your project.

---

### 🧱 Basic Structure

```hcl
terraform {
  required_version = ">= 1.4.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket = "my-terraform-state"
    key    = "global/s3/terraform.tfstate"
    region = "us-east-1"
  }
}
```

---

### 🔍 Common Settings

| Setting              | Purpose                                |
| -------------------- | -------------------------------------- |
| `required_version`   | Restricts Terraform version usage      |
| `required_providers` | Declares providers + versions          |
| `backend`            | Defines where state is stored          |
| `cloud`              | Connects to Terraform Cloud/Enterprise |

---

### ☁️ Example with Terraform Cloud

```hcl
terraform {
  cloud {
    organization = "my-org"

    workspaces {
      name = "my-workspace"
    }
  }
}
```

---

### 📋 Settings Summary

| Feature           | Description                                 |
| ----------------- | ------------------------------------------- |
| `terraform` block | Controls project-wide settings              |
| Version lock      | Prevents mismatch errors                    |
| Provider setup    | Ensures correct plugins                     |
| State backend     | Remote state storage                        |
| Cloud             | Integration with Terraform Cloud/Enterprise |

---

## 📥 Input Variables in Terraform

✅ **What are Input Variables?**
Variables are like **parameters** — they make code **reusable & configurable** instead of hardcoding values.

---

### 🧱 Declaring Variables

```hcl
variable "image_id" {
  type = string
}
```

---

### 💡 Examples

**Simple String**

```hcl
variable "image_id" {
  type = string
}
```

**List of Strings**

```hcl
variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
}
```

**List of Objects**

```hcl
variable "docker_ports" {
  type = list(object({
    internal = number
    external = number
    protocol = string
  }))
  default = [
    {
      internal = 8300
      external = 8300
      protocol = "tcp"
    }
  ]
}
```

---

### 🧾 Common Arguments in `variable` Block

| Argument      | Purpose                                       |
| ------------- | --------------------------------------------- |
| `default`     | Optional value                                |
| `type`        | Data type (`string`, `list`, `map`, `object`) |
| `description` | Adds documentation                            |
| `validation`  | Rules for allowed values                      |
| `sensitive`   | Hides values in logs/UI                       |

---

### ✅ Why Use Variables?

* Make code **reusable & modular**
* Avoid **hardcoding** values
* Pass dynamic values via CLI, `.tfvars`, or environment variables

---

## 📂 Terraform Variable Definition Files (.tfvars)

✅ **What are `.tfvars` files?**
Files used to assign values to declared variables.

📌 Useful for **environment separation** (e.g., dev, prod).

---

### 📄 Common File Types

| File Name          | Auto-loaded? | Description             |
| ------------------ | ------------ | ----------------------- |
| `terraform.tfvars` | ✅ Yes        | Default file            |
| `*.auto.tfvars`    | ✅ Yes        | Loaded automatically    |
| `custom.tfvars`    | ❌ No         | Must be loaded manually |
| `*.tfvars.json`    | ✅ Yes        | JSON format             |

---

### 🧾 Example

**terraform.tfvars**

```hcl
instance_type = "t3.medium"
region        = "us-east-1"
```

**prod.auto.tfvars**

```hcl
instance_count = 3
environment    = "prod"
```

---

### ▶️ How to Use

**Auto-loaded**

```bash
terraform apply
```

➡️ Picks up `terraform.tfvars` or any `*.auto.tfvars`.

**Manual load**

```bash
terraform apply -var-file="custom.tfvars"
```

---

## 🌍 Variables via Environment Variables (TF\_VAR\_)

✅ Terraform supports variables from **environment variables**.

---

### 🧩 Format

```
TF_VAR_<variable_name>=<value>
```

---

### ✅ Example

```bash
export TF_VAR_region="us-west-2"
terraform apply
```

Equivalent to:

```hcl
variable "region" {}
```

---

### 📋 Summary

| What    | How it works                   |
| ------- | ------------------------------ |
| Format  | `TF_VAR_<variable>=<value>`    |
| Set in  | Terminal or script             |
| Maps to | Terraform variable             |
| Used in | plan/apply                     |
| Benefit | Great for secrets & automation |

---

## 🔄 Loading Input Variables – Order & Sources

Terraform loads variables in a specific **priority order**.

---

### 1. **Default Autoloaded File**

* `terraform.tfvars` → Loaded automatically.

### 2. **Custom Variable Files**

* `dev.tfvars`, `prod.tfvars` → Must load with `-var-file`.

```bash
terraform apply -var-file="dev.tfvars"
```

### 3. **Autoloaded `.auto.tfvars`**

* Any file ending with `.auto.tfvars` → Auto-loaded.

### 4. **Inline CLI Variables**

```bash
terraform apply -var="ec2_type=t2.medium"
```

### 5. **Environment Variables**

```bash
export TF_VAR_instance_type="t2.micro"
```

---

### 🧠 Variable Loading Priority

| Method                | Autoloaded? | Command Needed? | Use Case             |
| --------------------- | ----------- | --------------- | -------------------- |
| `terraform.tfvars`    | ✅ Yes       | ❌ No            | Default values       |
| `*.auto.tfvars`       | ✅ Yes       | ❌ No            | Project-wide config  |
| Custom `.tfvars` file | ❌ No        | ✅ `-var-file`   | Env-specific         |
| Inline `-var`         | ❌ No        | ✅ `-var`        | Quick overrides      |
| Environment `TF_VAR_` | ✅ Auto      | ❌ No            | Secrets & automation |

---

Got it 👍 Here’s **only what you gave**, formatted cleanly into `README.md` style:

````markdown
# 🥇 Terraform Variable Definition Precedence (✅ Correct Order)

---

## ✅ What is Precedence?

When the same variable is defined in multiple places, Terraform uses the **highest-priority** value — and **ignores the rest**.

---

## 🔝 Correct Precedence (from Highest to Lowest)

| Priority | Source                                           | Example                                   |
| -------- | ------------------------------------------------ | ----------------------------------------- |
| 1️⃣      | **CLI flags** `-var` and `-var-file`             | `terraform apply -var="region=us-west-2"` |
| 2️⃣      | **`*.auto.tfvars` / `*.auto.tfvars.json`**       | Automatically loaded files                |
| 3️⃣      | **`terraform.tfvars` / `terraform.tfvars.json`** | Default variable file                     |
| 4️⃣      | **Environment variables** (`TF_VAR_<name>`)      | `export TF_VAR_region=us-east-1`          |
| 5️⃣      | **Default value in `variable` block**            | `default = "ap-south-1"`                  |

---

## 🧪 Example

```hcl
# variables.tf
variable "region" {
  default = "ap-south-1"
}
````

```bash
export TF_VAR_region="us-east-1"
terraform apply -var-file="prod.tfvars" # region = "us-west-1"
terraform apply -var="region=eu-west-3"
```

👉 **Final value used:** `eu-west-3` (because CLI `-var` wins)

---

## 📌 Summary Table

| Source Type                    | Loaded Automatically? | Priority  |
| ------------------------------ | --------------------- | --------- |
| `-var` / `-var-file`           | ❌ No (manual)         | 🔝 High   |
| `*.auto.tfvars(.json)`         | ✅ Yes                 | 🔼        |
| `terraform.tfvars(.json)`      | ✅ Yes (default)       | 🔽        |
| `TF_VAR_` environment variable | ❌ No (shell/script)   | 🔽        |
| `default` in variable block    | ✅ Yes (fallback only) | 🔽 Lowest |

---

# 📤 Terraform Output Values – Easy Explanation

---

## ✅ What Are Output Values?

Terraform **output values** are used to **display results** after a successful `apply`.

They help you:

* Get details like **instance IP**, **bucket name**, etc.
* Pass values to other configurations (via remote state).
* Use in automation or external scripts.

---

## 🧾 Syntax Example (from the screenshot)

```hcl
output "db_password" {
  value       = aws_db_instance.db.password
  description = "The password for logging in to the database."
  sensitive   = true
}
```

---

### 🔍 Explanation:

| Property      | Meaning                                                                   |
| ------------- | ------------------------------------------------------------------------- |
| `value`       | The actual data to output (from a resource, variable, etc.)               |
| `description` | *(Optional)* Text to describe the output                                  |
| `sensitive`   | *(Optional)* If `true`, **hides output in CLI** (but still in state file) |

---

## 🛑 Sensitive Outputs

* When `sensitive = true`, the output:

  * ❌ **Does NOT show in CLI** (`terraform apply`, `terraform output`)
  * ✅ **Still exists in the `.tfstate` file** (so use with care!)

---

## 🧠 Common Use Cases

* Expose IP address, DNS, or secrets (carefully!)
* Pass values between modules or workspaces
* Provide automation-friendly outputs to scripts

---

## 🧪 Example Use

```bash
terraform output db_password
# If sensitive = true → won't display anything
```

---

# 📦 Scenario: You Have 2 Terraform Projects

---

### ✅ Project A: **Creates a VPC**

It creates a VPC and outputs its ID:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

output "vpc_id" {
  value = aws_vpc.main.id
}
```

---

### ✅ Backend (in Project A) stores state remotely (like in S3):

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-bucket"
    key    = "network/terraform.tfstate"
    region = "us-east-1"
  }
}
```

State file location:

```
s3://my-terraform-bucket/network/terraform.tfstate
```

---

# 📥 Project B: **Wants to Reuse That VPC ID**

```hcl
data "terraform_remote_state" "network" {
  backend = "s3"
  config = {
    bucket = "my-terraform-bucket"
    key    = "network/terraform.tfstate"
    region = "us-east-1"
  }
}

resource "aws_subnet" "example" {
  vpc_id     = data.terraform_remote_state.network.outputs.vpc_id
  cidr_block = "10.0.1.0/24"
}
```

---

# ✅ A. Using `terraform_remote_state` with Local Backend

```hcl
data "terraform_remote_state" "vpc" {
  backend = "local"
  config = {
    path = "../vpc-project/terraform.tfstate"
  }
}
```

---

# ✅ B. Using `terraform_remote_state` with Terraform Cloud

```hcl
data "terraform_remote_state" "network" {
  backend = "remote"

  config = {
    organization = "my-org"
    workspaces = {
      name = "network-infra"
    }
  }
}
```

---

## 📋 Summary Table

| Backend Type    | How to Connect                          |
| --------------- | --------------------------------------- |
| Local backend   | `path = "../project/terraform.tfstate"` |
| S3 backend      | `bucket`, `key`, `region`               |
| Terraform Cloud | `organization`, `workspaces.name`       |

---

# 📤 Terraform Output Commands – Easy Explanation (No Code)

---

## ✅ What Are Output Commands?

Terraform output commands help you **view the results** of your infrastructure deployment — like resource IDs, IPs, or custom values.

---

## 🔧 Common Commands

* **`terraform output`** → Shows **all outputs**
* **`terraform output <name>`** → Shows one output
* **`terraform output -raw <name>`** → Raw value only
* **`terraform output -json`** → Outputs in JSON

---

# 🧠 Terraform Locals – Easy Explanation

---

## ✅ What Are Locals?

Locals let you define **named values** for reuse within a module.
They are **not inputs** — just internal shortcuts.

---

## 🧠 Use Cases

* Avoid repeating values
* Build naming conventions
* Reusable tags

---

## 📝 Summary

| Feature | Details                              |
| ------- | ------------------------------------ |
| Scope   | Only within current module           |
| Input?  | ❌ No                                 |
| Purpose | Internal reuse, simplify expressions |

---

# 🔁 Terraform Variables vs Locals vs Outputs

| Feature  | `variable`        | `local`                 | `output`                      |
| -------- | ----------------- | ----------------------- | ----------------------------- |
| Purpose  | Accept **inputs** | Internal reusable value | Show/share results            |
| Input    | ✅ Yes             | ❌ No                    | ❌ No                          |
| Output   | Used in config    | Used in module          | Shown after `apply`, in state |
| Scope    | Global            | Module only             | Global (exposed externally)   |
| Use Case | User config       | Avoid repetition        | Share IPs, IDs, secrets       |

---

# 🔍 Terraform `local` Values – Screenshot Explanation

* Use **`local.<name>`** to reference a local.
* Example: `tags = local.common_tags`
* Locals reduce repetition (DRY principle).
* Use **sparingly** — too many locals make configs hard to read.

---

