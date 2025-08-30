
# 🐛 Debugging Terraform

Terraform provides **logging and crash reports** to help troubleshoot issues. These are controlled mainly through environment variables and automatic crash logging.

---

## 🔧 Debug Logs with Environment Variables

Terraform can generate logs with different verbosity levels.

### Key Environment Variables

* **`TF_LOG`** – Controls overall logging verbosity.

  * Values (most → least verbose):
    `TRACE` → `DEBUG` → `INFO` → `WARN` → `ERROR` → `JSON` (same as TRACE but machine-readable).

* **`TF_LOG_CORE`** – Logging specifically for the **Terraform core engine**.

* **`TF_LOG_PROVIDER`** – Logging for **provider-specific operations**.

* **`TF_LOG_PATH`** – File path where logs are written. If unset → logs go to terminal.

### Example (Linux/macOS)

```sh
export TF_LOG=DEBUG
export TF_LOG_PATH=terraform.log
terraform plan
```

➡️ Creates a file `terraform.log` with debug details.

**Windows PowerShell** example:

```powershell
$env:TF_LOG="DEBUG"
$env:TF_LOG_PATH="terraform.log"
terraform apply
```

📌 **Tip**: Always reset/unset `TF_LOG` after debugging, otherwise Terraform commands will stay verbose.

---

## 💥 Crash Logs

Since Terraform is written in **Go**, if an internal bug causes a runtime panic, Terraform automatically creates a **`crash.log`**.

### What Happens on Crash?

* Go runtime triggers panic (e.g., invalid memory access).
* Terraform writes:

  * Debug logs
  * Panic message
  * Full **stack trace**
* Saved in:

  * Same working directory where the command was run.

📁 Example:
If running `terraform apply` in `/home/user/project/`, the log will appear as:

```
/home/user/project/crash.log
```

---

## 📋 Viewing crash.log

* **Linux/macOS**: `cat crash.log` or `less crash.log`
* **Windows**: `notepad crash.log`
* Last 20 lines only:

  ```sh
  tail -n 20 crash.log
  ```

---

## 🐞 Reporting a Crash

Terraform is open source (by HashiCorp). Report issues on GitHub:
👉 [https://github.com/hashicorp/terraform/issues](https://github.com/hashicorp/terraform/issues)

Include:

* Description of what caused the crash.
* Terraform version (`terraform -version`).
* OS/environment details.
* Attach `crash.log` (⚠️ review for sensitive info first).

---

## ✅ Summary

| Feature    | Description                                                                          |
| ---------- | ------------------------------------------------------------------------------------ |
| Debug logs | Controlled with env vars (`TF_LOG`, `TF_LOG_CORE`, `TF_LOG_PROVIDER`, `TF_LOG_PATH`) |
| Crash logs | Auto-created on panic (`crash.log` in working directory)                             |
| User role  | Use debug logs yourself, but forward crash logs to developers                        |
| Safety tip | Always sanitize crash logs before sharing                                            |

---

⚡ Extra Important Info (added):

* `TF_LOG` should not be left enabled in production (too verbose, may expose sensitive data).
* Crash logs are **developer-facing**, not for user troubleshooting.
* If using automation/CI pipelines → redirect logs to files for easier troubleshooting (`TF_LOG_PATH`).

---

Would you like me to also add a **1-page cheat sheet table** combining all Terraform logging + crash info (so it’s easy to memorize for certification)?
