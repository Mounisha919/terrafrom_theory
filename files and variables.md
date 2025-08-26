

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

✅ Now you have **all Terraform settings & variable concepts in one place**.

Would you like me to **add practical examples** (e.g., deploying an AWS EC2 instance using `terraform` block + variables + tfvars) so this becomes a hands-on mini project?
