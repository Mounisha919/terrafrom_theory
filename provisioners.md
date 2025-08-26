# 🔧 Terraform Provisioners

* Provisioners in Terraform let you run custom actions (like shell scripts, file copies, or configuration commands) on your infrastructure after Terraform creates it.
* After Terraform creates a virtual machine (VM), **provisioners** can:

  * Install software (e.g., Apache, Nginx)
  * Run scripts (e.g., `bash`)
  * Copy or edit files on the server

🗣 *“Provisioners help me configure the VM after it's created.”*

---

### 🔄 So What About `cloud-init` and `Packer`?

| Tool         | Role                  | Part of Terraform?            | How it connects                           |
| ------------ | --------------------- | ----------------------------- | ----------------------------------------- |
| `cloud-init` | OS boot-time config   | ❌ Not a Terraform provisioner | Used via `user_data` in VM resource       |
| `Packer`     | Builds machine images | ❌ Separate tool               | Use Packer before Terraform to build AMIs |

---

> ❌ `cloud-init` and `Packer` are **not provisioners**, but tools/features used *with* Terraform.

---

### ☁️ **Cloud-Init**

* Cloud-Init is a tool used when creating cloud VMs (AWS, Azure, etc.).
* You give it a script (like `bash` or YAML).
* That script **runs when the VM starts for the first time**.

🗣 *“Cloud-Init runs a script automatically when the VM boots — like installing updates.”*

🖼️ **Example in image:** This script installs Apache (httpd) and starts it on the VM.

resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  user_data = <<-EOF
              #!/bin/bash
              apt update
              apt install nginx -y
              EOF
}

---

### 📦 **Packer**

🖼️ From image:

> "Packer is an automated image-builder service.
> You provide a configuration file to create and provision the machine image and the image is delivered to a repository for use."

✅ **Simple Explanation:**

* Packer is used to **pre-build full machine images**.
* You write a configuration file → it creates an image with all software/tools installed → this image is ready to launch.
* Works great with Terraform when you want fast, **pre-baked machines**.

🗣 *“Packer creates a ready-made machine image I can reuse anytime in Terraform.”*

---
🔗 How to Use Them Together

First, use Packer to build an image:
{
  "builders": [{
    "type": "amazon-ebs",
    "ami_name": "nginx-server-{{timestamp}}",
    "source_ami": "ami-0abcdef1234567890",
    "instance_type": "t2.micro",
    ...
  }],
  "provisioners": [{
    "type": "shell",
    "inline": ["sudo apt update", "sudo apt install nginx -y"]
  }]
}


Then, in Terraform, refer to that image:

resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxxxxxx" # ID from Packer output
  instance_type = "t2.micro"
}

---

### ⚙️ Terraform Provisioner: `local-exec`

> It lets you **run shell or CLI commands on the machine** where you're running `terraform apply`**after** it finishes creating a resource (like a VM, bucket, etc.)..

✅ This could be:

# Local Environments Could Be:

* **Local Machine** – Your laptop/workstation
* **Build Server** – Jenkins, GitHub Actions, AWS CodeBuild, etc.
* **Terraform Cloud** – Terraform’s own cloud execution environment

---

### 📦 Real-world Use Case Example (from image):

> After you provision a VM, you may want to **send the VM's public IP to a third-party tool** (like a monitoring system or firewall).
> Adding a newly created VM's IP to a security tool
> Creating a log or record
> Triggering a local script for further automation
> Notify systems like Slack or Email when infra is deployed
> Register the instance's IP in DNS or firewall

---

### 🔁 Outputs vs `local-exec`

* `outputs`: used to **display** values.
* `local-exec`: used to **run commands** using those values (like automation scripts).

---

### ✅ Summary Table

| Feature                | Description                                    |
| ---------------------- | ---------------------------------------------- |
| **Purpose**            | Run local shell commands after provisioning    |
| **Runs On**            | The machine running `terraform apply`          |
| **Use Case**           | Notify systems, run scripts, update config     |
| **Examples**           | `echo`, `curl`, `scp`, CLI tools               |
| **Compare to Outputs** | Outputs **display**, `local-exec` **executes** |

---

### ⚙️ `local-exec` Parameters and Examples (from Screenshot):

---

#### ✅ `command` (required)

> **The command you want to execute**

📦 Example:

```hcl
provisioner "local-exec" {
  command = "echo ${self.private_ip} >> private_ips.txt"
}
```

⏩ This stores the private IP of the instance into a file.

---

#### 📁 `working_dir` (optional)

> **Directory where the command will be executed**

🧾 Example:
`/user/andrew/home/project`
Use this when the command depends on local files or scripts in a specific folder.

---

#### 💻 `interpreter` (optional)

> **Which program will run the command**

🧾 Example:

```hcl
interpreter = ["PowerShell", "-Command"]
command     = "Get-Date >> completed.txt"
```

This will use PowerShell to run the command.

You can also use:

* `"bash"` on Linux/Mac
* `"cmd"` on Windows
* `"python"` or other scripting tools

---

#### 🌍 `environment` (optional)

> **Set environment variables used in the command**

🧾 Example:

```hcl
environment = {
  KEY = "12345"
  SECRET = "67890"
}
command = "echo $KEY $SECRET >> credentials.yml"
```

This helps in **hiding sensitive data** or **reusing values** securely.

---

### 🛰️ Terraform Provisioner: Remote-Exec

## 📌 What is `remote-exec`?

`remote-exec` is a **Terraform provisioner** that allows you to **execute commands on a target resource *after* it has been provisioned**.

---

## 📊 Visual Overview

```
Local Machine                   remote-exec                  Provisioned VM
executing Terraform  -------------------------->  executing provided commands/script
```

* **Local Machine**: Runs your Terraform code.
* **Provisioned VM**: The infrastructure resource created by Terraform (e.g., EC2, Azure VM).
* **remote-exec**: Executes post-provisioning shell commands/scripts **on the remote resource**.

---

## ✅ Use Case

* Ideal for running **simple provisioning scripts** like installing packages, updating OS, setting environment variables, etc.
* For example:

  ```hcl
  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install nginx -y"
    ]
  }
  ```

---

## ⚠️ Recommendation from Experts

> **For more complex tasks**, it's recommended to use **Cloud-Init**
> and strongly recommended in all cases to **bake Golden Images** via tools like:

* 🧁 **Packer**
* 📷 **EC2 Image Builder**

These tools help you **pre-build VMs/images** with everything already set up, which is:

* More reliable ✅
* Repeatable 🔁
* Scalable across environments 📈

---

**Three different modes of `remote-exec`** provisioner in **Terraform**, explained clearly and concisely:

---

# 🧰 Terraform `remote-exec` Provisioner – Modes Explained

The `remote-exec` provisioner in Terraform allows you to execute **scripts or commands** on a remote resource (like an EC2 instance) **after it's created**.

Terraform supports **3 modes** of remote-exec connection:

---

## 🔧 1. `inline` Mode

* **Description**: Run commands directly written inside the Terraform configuration.
* **Use Case**: Useful for quick one-liner setup or basic configuration steps.

```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt-get update",
    "sudo apt-get install -y nginx"
  ]
}
```

📝 *Each command is executed sequentially as if typed in a shell.*

---

## 📜 2. `script` Mode

* **Description**: Runs a **local shell script file** on the remote machine.
* **Use Case**: Ideal when your setup logic is too complex for inline commands.

```hcl
provisioner "remote-exec" {
  script = "setup.sh"
}
```

📝 *The `setup.sh` script must be present on your local machine and is copied to the remote instance before execution.*

---

## 📁 3. `scripts` Mode (with `scripts = []`)

* **Description**: (Unofficial/Custom) Refers to chaining multiple script files.
* **Use Case**: Not directly supported by Terraform core, but users implement it using multiple `remote-exec` blocks or `null_resource`.

Example using multiple provisioners:

```hcl
provisioner "remote-exec" {
  script = "setup-part1.sh"
}

provisioner "remote-exec" {
  script = "setup-part2.sh"
}
```

📝 *Used for breaking down scripts into manageable chunks or logical steps.*

---

## 🔐 Connection Block Example

Each mode requires a `connection` block to define **SSH access details**:

```hcl
connection {
  type        = "ssh"
  user        = "ubuntu"
  private_key = file("~/.ssh/id_rsa")
  host        = self.public_ip
}
```

---

## ✅ Summary

| Mode      | Key Field         | Used For                          | Example Use                 |
| --------- | ----------------- | --------------------------------- | --------------------------- |
| inline    | `inline`          | Quick shell commands              | `apt update`, `yum install` |
| script    | `script`          | Run full shell scripts            | `setup.sh`, `init_db.sh`    |
| scripts\* | multiple `script` | Modular, multiple script chaining | `part1.sh`, `part2.sh`      |

> ⚠️ *scripts\[] is not an official mode, but a user pattern via multiple `remote-exec`.*

---

### 📦 Terraform `file` Provisioner – Modes Explained

The `file` provisioner is used to **upload files or directories** from your **local machine to a remote resource** (like a VM or EC2 instance) **after it's created**.

Terraform supports **3 common usage modes** for the `file` provisioner:

---

## 📁 1. **Single File Upload**

* **Description**: Copies a single file from your local system to a remote path.
* **Use Case**: Upload configuration files, credentials, scripts, etc.

```hcl
provisioner "file" {
  source      = "app.conf"
  destination = "/etc/app.conf"
}
```

📝 *Copies `app.conf` from local to the remote system.*

---

## 📂 2. **Directory Upload**

* **Description**: Copies a **whole folder recursively**.
* **Use Case**: When you need to transfer multiple files, app code, or complex configurations.

```hcl
provisioner "file" {
  source      = "app_folder/"
  destination = "/opt/app/"
}
```

📝 *The trailing `/` ensures directory content is copied, not the folder itself.*

---

## 🛠️ 3. **File Templating Before Upload** (using `templatefile()` + `file`)

* **Description**: Render a template file (e.g., `*.tpl`) using variables before uploading it.
* **Use Case**: When the uploaded file needs to be dynamically customized (e.g., hostname, environment).

```hcl
data "template_file" "config" {
  template = file("${path.module}/config.tpl")
  vars = {
    env  = "production"
    port = 8080
  }
}

resource "null_resource" "upload_config" {
  provisioner "file" {
    content     = data.template_file.config.rendered
    destination = "/etc/myapp/config.cfg"
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = self.public_ip
  }
}
```

📝 *This approach uses dynamic content instead of static files.*

---

## 🔐 Connection Block Example

The `file` provisioner also requires a connection block (like `remote-exec`):

```hcl
connection {
  type        = "ssh"
  user        = "ubuntu"
  private_key = file("~/.ssh/id_rsa")
  host        = self.public_ip
}
```

---

## ✅ Summary

| Mode                  | Key Fields               | Used For                            | Notes                                       |
| --------------------- | ------------------------ | ----------------------------------- | ------------------------------------------- |
| Single File Upload    | `source`, `destination`  | Copy one file                       | Simple and fast                             |
| Directory Upload      | `source` (folder path)   | Copy multiple files or whole folder | Use trailing `/` for folder content         |
| Templated File Upload | `content`, `destination` | Upload rendered dynamic config      | Needs `templatefile()` + `file provisioner` |

---

**Types of `connection` blocks** in Terraform:

---

# 🔌 Terraform `connection` Block – Types Explained

The `connection` block in Terraform defines **how Terraform connects** to the remote resource (e.g., EC2, VM) to run **provisioners** like `remote-exec` or `file`.

There are **2 main connection types**:

---

## 1️⃣ `ssh` – For Linux/Unix Machines

* **Used For**: Connecting to Linux-based virtual machines (EC2, GCP VM, Azure VM, etc.)
* **Protocol**: Secure Shell (SSH)
* **Authentication**: Private key or password

```hcl
connection {
  type        = "ssh"
  user        = "ubuntu"
  private_key = file("~/.ssh/id_rsa")
  host        = self.public_ip
}
```

🔑 **Alternate option (password-based)**:

```hcl
connection {
  type     = "ssh"
  user     = "ubuntu"
  password = "yourpassword"
  host     = self.public_ip
}
```

---

## 2️⃣ `winrm` – For Windows Machines

* **Used For**: Connecting to **Windows** virtual machines
* **Protocol**: Windows Remote Management (WinRM)
* **Authentication**: Username and password

```hcl
connection {
  type     = "winrm"
  user     = "Administrator"
  password = "YourPassword123!"
  host     = self.public_ip
  port     = 5986
  https    = true
  insecure = true
}
```

🛡️ *Port 5986 is default for HTTPS WinRM. `insecure = true` is often required for self-signed certs.*

---

## ✅ Summary Table

| Type    | Used For       | Protocol | Auth Method             | Notes                                |
| ------- | -------------- | -------- | ----------------------- | ------------------------------------ |
| `ssh`   | Linux/Unix VMs | SSH      | Private key or password | Most commonly used                   |
| `winrm` | Windows VMs    | WinRM    | Username + Password     | Requires port 5986 and WinRM enabled |

---

### 🧱 Terraform `null_resource` 

The `null_resource` in Terraform is a **resource with no infrastructure** attached to it. It is mainly used to run **provisioners**, **local-exec**, or **remote-exec** scripts for custom tasks.

> 📌 It doesn't create any cloud resource (like EC2, S3, etc.) — it just runs actions during apply.

---

## 🚀 1. Basic Usage

```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Hello, Terraform!"
  }
}
```

📝 *This just prints a message during `terraform apply`.*

---

## ⚙️ 2. With Triggers (to force re-execution)

* **Use Case**: When you want the `null_resource` to run again if input values change.

```hcl
resource "null_resource" "restart_service" {
  triggers = {
    script_version = "v1.2.3"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo systemctl restart myapp"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = aws_instance.app_server.public_ip
  }
}
```

📝 *Changing `script_version` will re-run the `null_resource`.*

---

## 🧰 3. Common Use Cases

| Use Case                                | Example                                           |
| --------------------------------------- | ------------------------------------------------- |
| Run provisioners without creating infra | Just want to copy a file or run a command         |
| Trigger actions based on file/version   | Use `triggers` to force re-run when input changes |
| Run local scripts or shell commands     | `local-exec` for local tasks during apply         |
| Upload files or configure remote        | Use with `remote-exec` or `file` provisioners     |

---

## 🧑‍💻 **Use Case: Configure a VM using Ansible after creation**

---

### 🧾 Scenario:

You created a **VM using Terraform**, but Terraform alone doesn’t do app setup (like installing Nginx).
You want to **run an Ansible playbook** to configure it **right after the VM is ready**.

---

## ✅ How to do it?

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c123456789abcde0"
  instance_type = "t2.micro"
  key_name      = "mykey"
}

resource "null_resource" "ansible_provision" {
  depends_on = [aws_instance.web]

  provisioner "local-exec" {
    command = "ansible-playbook -i ${aws_instance.web.public_ip}, setup.yml"
  }
}
---

## ✅ Summary

| Feature           | Description                                  |
| ----------------- | -------------------------------------------- |
| No real resource  | Doesn't create infra like EC2, S3, etc.      |
| Runs provisioners | Supports `file`, `local-exec`, `remote-exec` |
| Use triggers      | Force re-run based on variable/file changes  |
| Good for scripts  | Custom tasks before/after resource creation  |

---


### **When should I use a `null_resource` with provisioners, and when should I use a provisioner directly inside a resource (like EC2)?**


# 🔍 Terraform Provisioners: `null_resource` vs. Inline in Resource

Terraform provisioners like `remote-exec`, `local-exec`, and `file` can be used in **two ways**:

1. ✅ Directly **inside a resource** (e.g., `aws_instance`)
2. ✅ Inside a **`null_resource`**

---

## ⚙️ Option 1: **Provisioner Inside a Resource Block**

### ✅ When to Use:

* Simple provisioning needed right **after** resource creation
* Provisioning logic is tightly coupled to a single resource
* You don't need advanced control over when provisioners re-run

### 📦 Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0abc123456"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y nginx"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = self.public_ip
  }
}
```

---

## 🧱 Option 2: **Provisioner with `null_resource`**

### ✅ When to Use:

* You want to **separate provisioning logic** from the resource
* You need to **control re-execution** using `triggers`
* You want to **reuse** the provisioner logic for multiple resources
* You want to run provisioners **on existing infrastructure**
* You want to **combine provisioners** (e.g., `file` + `remote-exec`) modularly

### 📦 Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0abc123456"
  instance_type = "t2.micro"
}

resource "null_resource" "setup" {
  provisioner "remote-exec" {
    inline = [
      "echo Hello from remote VM"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("~/.ssh/id_rsa")
    host        = aws_instance.web.public_ip
  }

  triggers = {
    script_version = "v1.0"
  }

  depends_on = [aws_instance.web]
}
```

---

## 🆚 Comparison Table

| Feature                         | Inline in Resource Block  | `null_resource` with Provisioner         |
| ------------------------------- | ------------------------- | ---------------------------------------- |
| 🔧 Simplicity                   | ✅ Simpler for small tasks | ❌ Slightly more complex                  |
| 🔄 Trigger-based re-run control | ❌ Not supported           | ✅ Yes, using `triggers`                  |
| 🧱 Separation of concerns       | ❌ Logic inside resource   | ✅ Clear separation of resource and logic |
| 🔁 Reusability of logic         | ❌ Resource-specific       | ✅ Easily reusable with variables         |
| ♻️ Provisioning existing infra  | ❌ Cannot be used          | ✅ Can use IP or reference                |
| ⚙️ Modular design               | ❌ Less modular            | ✅ Fully modular and testable             |

---

## 📝 Summary: When to Use Which?

| Use Case                                   | Use This                        |
| ------------------------------------------ | ------------------------------- |
| Simple, one-time setup after creating a VM | Inline Provisioner              |
| You need re-run when version/file changes  | `null_resource` with `triggers` |
| Use with existing infrastructure           | `null_resource`                 |
| Modular or reusable provisioning scripts   | `null_resource`                 |
| Complex logic like multiple provisioners   | `null_resource`                 |

---
Here’s a clean, **README-style explanation** of `terraform_data` vs `null_resource` in Terraform — beginner-friendly and ready for documentation or interviews:

---

# 📘 Terraform: `terraform_data` vs `null_resource` — Easy Explanation

## ✅ 1. What is `null_resource`?

### 🔹 Definition:

A **dummy Terraform resource** that performs **side tasks** like running scripts, copying files, or executing shell commands.

### 🔧 Used For:

* Running `local-exec`, `remote-exec`, or `file` provisioners
* Post-deployment scripts (e.g., installing packages on a VM)
* Workarounds for Terraform limitations
* Force re-execution with `triggers`

### 🔧 Example:

```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Hello from null_resource"
  }

  triggers = {
    always_run = timestamp()
  }
}
```

---

## ✅ 2. What is `terraform_data`?

### 🔹 Definition:

A **modern built-in resource** (introduced in Terraform v1.4) designed to **replace `null_resource`** for executing provisioners in a more structured and idiomatic way.

### 🔧 Used For:

* Same use cases as `null_resource`
* Cleaner structure using **`input`** instead of **`triggers`**
* Recommended for **new Terraform code**

### 🔧 Example:

```hcl
resource "terraform_data" "example" {
  input = {
    script_version = "1.0.0"
  }

  provisioner "local-exec" {
    command = "echo Hello from terraform_data"
  }
}
```

---

## 📊 Easy Difference Table

| Feature                     | `null_resource`          | `terraform_data`           |
| --------------------------- | ------------------------ | -------------------------- |
| ✅ Purpose                   | Run provisioners/scripts | Run provisioners/scripts   |
| 🛠️ Creates real infra?     | ❌ No                     | ❌ No                       |
| 📦 Introduced in            | Old (pre-1.0)            | ✅ Terraform v1.4+          |
| 🔁 Re-run on change?        | Yes (via `triggers`)     | Yes (via `input`)          |
| 📐 Input Structure          | String map               | Structured object          |
| 🚀 Recommended for new use? | ❌ No                     | ✅ Yes                      |
| 🧪 Ideal for                | Legacy or quick hacks    | Modern, clean provisioning |

---

## 💡 When to Use What?

| Scenario                                          | Use This                           |
| ------------------------------------------------- | ---------------------------------- |
| Terraform version ≥ 1.4                           | ✅ `terraform_data`                 |
| You want cleaner, structured configuration        | ✅ `terraform_data`                 |
| Maintaining old codebase                          | ✅ `null_resource`                  |
| Need to rerun based on file/script/version change | ✅ Both (use `input` or `triggers`) |

---

## 🧠 Super Easy Summary

| Question                        | Answer              |
| ------------------------------- | ------------------- |
| Do both run scripts/commands?   | ✅ Yes               |
| Do they create cloud resources? | ❌ No                |
| Which one is newer & better?    | ✅ `terraform_data`  |
| Is `null_resource` still valid? | ✅ Yes (legacy only) |

---

## 📁 Notes for Usage

* **Provisioners** inside both can be:

  * `local-exec` (run command locally)
  * `remote-exec` (run on remote VM)
  * `file` (copy file to remote)

* If you need to **force re-run**, use:

  * `triggers` in `null_resource`
  * `input` value change in `terraform_data`

---

# 📘 Terraform `triggers` – Easy Guide

## 🎯 What is `triggers`?

* In Terraform, the `triggers` argument (inside a `null_resource`) tells Terraform **when to re-run the provisioner**, even if the resource itself has not changed.

---

## 🤔 Why use it?

* By default, provisioners run **only once** (first `apply`).
* Use `triggers` when you need Terraform to re-run because of:

  * Script updates
  * Version changes
  * Manual force execution

---

## 🛠️ Example Without `triggers`

```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Running setup"
  }
}
```

🔁 Runs **only once**.
❌ Changing a file or variable later will NOT re-run.

---

## ✅ Example With `triggers`

```hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Running setup"
  }

  triggers = {
    version = "v1.0"
  }
}
```

🎯 If you change `v1.0` → `v1.1`, Terraform detects the change and re-runs the provisioner.

---

## 💡 Trigger on File Changes

```hcl
triggers = {
  script_hash = filesha1("setup.sh")
}
```

✅ Re-runs provisioner **only when the script file changes**.

---

## 📋 Summary Table

| Feature          | What It Does                           |
| ---------------- | -------------------------------------- |
| `triggers` block | Forces a `null_resource` to re-run     |
| Key-Value pairs  | Any change = re-run triggered          |
| Common uses      | Versioning, file changes, manual force |
| Works in         | `null_resource` only (not others)      |

---

## 🗣️ Interview-Friendly Answer

👉 *“In Terraform, the `triggers` argument inside a `null_resource` is used to re-run provisioners whenever specified values change, such as version numbers or file hashes. It’s mainly used for tasks like re-running scripts on file changes, version updates, or forcing execution when needed.”*

---

