# Config_manager_comp
Genral Configuration Manager
Absolutely — here's a **cleaned-up, general-purpose version** of your code using Dynaconf, without any domain-specific names or sensitive data (PII). This version shows **how Dynaconf is structured and used**, in a way that can be reused in any generic project.

---

## ✅ General-Purpose Dynaconf Example

### 📁 Project Structure

```
project/
├── config/
│   ├── app.yaml
│   ├── cloud.yaml
│   ├── database.yaml
│   └── secrets.yaml   # (Optional)
├── main.py
```

---

### 📄 `config/app.yaml`

```yaml
model_type: "gpt-like"
features:
  enable_feature_x: true
```

---

### 📄 `config/cloud.yaml`

```yaml
cloud_details:
  sample_domain:
    provider: "aws"
    region: "us-east-1"
```

---

### 📄 `config/database.yaml`

```yaml
database_details:
  sample_domain:
    host: "localhost"
    port: 5432
```

---

### 📄 `config/secrets.yaml` (Optional)

```yaml
cloud_secrets:
  sample_domain:
    api_key: "REPLACE_ME"
```

---

### 🧠 General Dynaconf Usage: `main.py`

```python
import os
from dynaconf import Dynaconf

# Load main configs
config = Dynaconf(
    envvar_prefix="APP",
    settings_files=[
        os.path.join("config", "app.yaml"),
        os.path.join("config", "cloud.yaml"),
        os.path.join("config", "database.yaml"),
    ]
)

# Load secrets (separate to avoid committing to version control)
try:
    secrets = Dynaconf(
        envvar_prefix="APP",
        settings_files=[os.path.join("config", "secrets.yaml")],
    )
except Exception:
    raise Exception("Secrets file not found")

# Domain you're configuring for
domain_name = "sample_domain"

# Access configuration values
model_type = config.model_type
cloud_config = getattr(config.cloud_details, domain_name)
db_config = getattr(config.database_details, domain_name)
cloud_secrets = getattr(secrets.cloud_secrets, domain_name)

# Output
print("Model Type:", model_type)
print("Cloud Provider:", cloud_config.provider)
print("Database Host:", db_config.host)
print("Cloud API Key:", cloud_secrets.api_key)
```

---

### ✅ Summary of How Dynaconf Is Used

| Feature                               | Description                                      |
| ------------------------------------- | ------------------------------------------------ |
| `Dynaconf(settings_files=...)`        | Loads config from YAML files                     |
| `getattr(config, "some_section")`     | Dynamically access nested configs                |
| `.as_dict()` or dot-access            | Convert configs or access directly               |
| Separate secrets loading              | Helps avoid checking secrets into source control |
| YAML structure mirrors code hierarchy | Helps maintain domain-based configs cleanly      |

---

Would you like this turned into a small GitHub-style starter repo or a zip of example files?

