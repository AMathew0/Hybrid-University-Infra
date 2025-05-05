# 🧭 Diagrams

This folder contains visual representations of the Hybrid University Infrastructure, built with [draw.io](https://app.diagrams.net/). These diagrams support architectural planning, documentation, and review across all infrastructure layers (cloud, on-prem, networking, security, etc.).


## 📁 Main Diagram

```

| File | Description |
|------|-------------|
| `master-architecture.drawio` | The latest master architecture diagram combining hybrid components. This file is always updated to reflect the most recent approved version. |

```

## 🗂️ Diagram Versioning System

A version-controlled folder tracks the history of diagram changes:

```

diagrams/
├── master-architecture.drawio
├── versions/
│   ├── master-architecture\_v0.1.drawio
│   ├── master-architecture\_v0.2.drawio
│   └── ...

```

## 🔖 Versioning Rules

```

| Version | Summary |
|---------|---------|
| `v0.1`  | Initial design: base hybrid layout |
| `v0.2`  | Added cloud-specific segmentation |
| `v0.3`  | Integrated on-prem servers and DR zones |
| `vX.Y`  | Follow semantic versioning: `Major.Minor` |

- **Edit** only versioned copies (inside `versions/`)
- **Overwrite** `master-architecture.drawio` with the new latest version once finalized
- Use **Git tags/releases** (e.g., `v0.3-diagrams`) to mark updates

```

---

## 🖼️ Optional: Diagram Exports

Use the `exports/` folder for read-only or presentation-ready formats:

```

diagrams/
├── exports/
│   ├── master-architecture\_v0.3.png
│   └── master-architecture\_v0.3.pdf

```

These exports are helpful for stakeholders, documentation, or offline access.

---

## 📝 How to View or Edit

- Open via [draw.io web editor](https://app.diagrams.net/)
- Or install the **Draw.io Desktop App**
- Always commit changes with meaningful messages (e.g., `added IAM + VPN to v0.3`)

---

For contextual details on what each section of the infrastructure does, refer to the [main project README](../README.md).
```
