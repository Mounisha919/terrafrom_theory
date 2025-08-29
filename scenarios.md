
# 🔢 Terraform `count`

* `count` lets you create **multiple copies** of a resource.

```hcl
resource "aws_instance" "server" {
  count = 4  # Creates 4 EC2s
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"

  tags = {
    Name = "Server ${count.index}"  # Server 0..3
  }
}
```

| Concept       | Explanation                      |
| ------------- | -------------------------------- |
| `count=4`     | 4 copies of resource             |
| `count.index` | Current index (0-based)          |
| Dynamic count | `count = length(var.subnet_ids)` |

💡 Use case: Deploy EC2s dynamically across subnets.

---

# 🔁 Terraform `for_each`

* Used for **multiple, slightly different resources**.
* Works with **maps** and **sets/lists**.

### Example with Map:

```hcl
resource "azurerm_resource_group" "rg" {
  for_each = {
    a_group = "eastus"
    b_group = "westus2"
  }
  name     = each.key
  location = each.value
}
```

### Example with Set/List:

```hcl
resource "aws_iam_user" "accounts" {
  for_each = toset(["Todd", "James", "Alice"])
  name     = each.key
}
```

| Feature | `count`             | `for_each`               |
| ------- | ------------------- | ------------------------ |
| Input   | Number/List         | Map or Set               |
| Index   | `count.index`       | `each.key`, `each.value` |
| Use     | Identical resources | Custom values            |

✅ Use `count` for duplicates, `for_each` for custom variations.

---

# 🛠️ Resource Behaviour in Terraform

When running `terraform apply`, resources may:

1. **Create** → New in config, not in state
2. **Destroy** → Exists in state, removed from config
3. **Update in-place** → Config changed, modifiable directly
4. **Destroy & Re-create** → Config changed, but not updatable in-place

---

# ⚡ Scenarios & Commands

## Scenario 1: Resource removed from `.tf` (still in state)

```bash
terraform init
terraform plan   # Marks resource for destroy
terraform apply  # Destroys removed resource
```

---

## Scenario 2: Resource deleted manually (still in config & state)

```bash
terraform init
terraform plan   # Detects drift / missing resource
terraform apply  # Recreates deleted resource
```

---

## Scenario 3: Resource deleted manually (also removed from config)

👉 If you **don’t want Terraform to recreate it**:

1. Delete the resource from your `.tf` file
2. Run:

```bash
terraform init
terraform plan   # Marks resource for destroy (since config is removed)
terraform apply  # Removes it from state (matches reality)
```

---

# 🔧 Helpful Commands

| Command                    | Purpose                             |
| -------------------------- | ----------------------------------- |
| `terraform state list`     | Show all tracked resources          |
| `terraform refresh`        | Sync state with actual infra        |
| `terraform state rm <res>` | Manually remove resource from state |

---

# ✅ Summary

* Use `count` for identical resources, `for_each` for unique ones.
* Terraform actions: **create, destroy, update, recreate**.
* Drift handling:

  * Deleted in config → destroy
  * Deleted in console → recreate
  * Deleted in both → remove from state only
* Always `terraform plan` before `apply` to preview.

---
