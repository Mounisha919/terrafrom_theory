
# 📘 Terraform Expressions (Quick Guide)

1. **Types & Values** → Define data types (string, number, bool, list, map).

```hcl
name = "app" ; size = 2 ; enabled = true
```

2. **Strings & Templates** → Combine text with variables.

```hcl
greet = "Hello, ${var.name}"
```

3. **References** → Access variables/resources.

```hcl
ami = var.ami_id
```

4. **Operators** → Do math or comparisons.

```hcl
double = var.count * 2
```

5. **Functions** → Use built-in helpers.

```hcl
zones_count = length(var.zones)
```

6. **Conditionals** → Choose value based on condition.

```hcl
type = var.env == "prod" ? "t3.large" : "t2.micro"
```

7. **For Expression** → Loop to create new values.

```hcl
servers = [for n in var.names : "${n}-server"]
```

8. **Splat Expression** → Get all values from a list.

```hcl
ids = aws_instance.web[*].id
```

9. **Dynamic Block** → Build nested blocks dynamically.

```hcl
for_each = [22, 80]   # opens ports
```

10. **Type Constraint** → Restrict variable type.

```hcl
variable "names" { type = list(string) }
```

11. **Version Constraint** → Control Terraform version.

```hcl
required_version = ">= 1.3.0"
```

---
Got it 👍 You want me to **add the real-world AWS tagging example** directly at the end of the README you shared.
Here’s the updated version with the extra example included:

---

# 📘 Strings Templates in Terraform

### 🧩 What is a String Template?

String templates in Terraform let you create **dynamic strings**.
They support:

* **Interpolation** → insert values into strings.
* **Directives** → add logic (if/else, loops).

---

### 🔸 1. String Interpolation

Insert variable values with `${}`.

```hcl
"Hello, ${var.name}!"
```

✔ Replaces `var.name` with the actual value.

---

### 🔸 2. String Directive

Add logic (if/else) inside strings with `%{}`.

```hcl
"Hello, %{ if var.name != "" }${var.name}%{ else }unnamed%{ endif }!"
```

✔ Prints the name if available, otherwise `"unnamed"`.

---

### 🔸 3. Templates with HEREDOC

Use `<<EOT ... EOT` for multi-line strings.

```hcl
<<EOT
%{ for ip in aws_instance.example.*.private_ip }
server ${ip}
%{ endfor }
EOT
```

✔ Loops over EC2 instance IPs →
`server 192.168.1.1`, `server 192.168.1.2`

---

### 🔸 4. Whitespace Stripping (\~)

Use `~` to remove extra spaces/newlines.

```hcl
<<EOT
%{ for ip in aws_instance.example.*.private_ip ~}
server ${ip}
%{ endfor ~}
EOT
```

✔ Keeps output clean without extra blank lines.

---

### 🧠 Summary

| Feature | Use                             |
| ------- | ------------------------------- |
| `${}`   | Insert variables or expressions |
| `%{}`   | Add logic (if/else, loops)      |
| `<<EOT` | Multi-line strings              |
| `~`     | Removes unwanted whitespace     |

---

