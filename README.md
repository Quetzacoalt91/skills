# PrestaShop Skills

Welcome to the **PrestaShop Skills** repository! This repository serves as a
centralized collection of AI "skills" designed to help you interact with,
manage, and develop for PrestaShop. These skills cover various domains including
PrestaShop modules, core components, and themes.

## 🗂️ Repository Structure

The repository is organized by **application domains**. Currently, the supported
domains include:

- `autoupgrade` (Module Update Assistant)

_(More domains like core, specific modules, and themes will be added over
time)._

Inside each application domain, skills are divided into two specific target
audiences:

- 🧑‍💻 **`user`**: Skills intended for store owners, merchants, and agencies.
  These allow you to perform operational actions on your PrestaShop store using
  AI (e.g., updating the store, checking compatibility, or restoring backups).
- 🛠️ **`dev`**: Skills intended for developers. These are tailored to help
  build, debug, and enhance the application domain itself.

**Directory tree example:**

```text
PrestaShop/skills/
├── autoupgrade/          # Domain
│   ├── user/             # User-facing operational skills
│   │   ├── prestashop-update/
│   │   ├── prestashop-restore/
│   │   └── ...
│   └── dev/              # Developer-facing skills
└── README.md             # This file
```

## 🚀 Installation & Usage

You can easily install the skills you need by using the `npx skills` CLI. The
command will list the available skills in the targeted directory and install
them directly into the compatible AI assistants present on your system.

**General Command syntax:**

```bash
npx skills install PrestaShop/skills/[application]/[user|dev]
```

**Installation Example:** To install operational user skills for the
`autoupgrade` module:

```bash
npx skills install PrestaShop/skills/autoupgrade/user
```

_(For more details on the `npx skills` commands and mechanics, please refer to
the [vercel-labs/skills repository](https://github.com/vercel-labs/skills))._

## 📚 Available Skills Inventory

As this repository grows, this list will be updated. Here is a summary of the
skills currently available:

### Domain: `autoupgrade`

| Category | Skill Folder              | Skill Name                    | Description                                                                                                                                                              |
| -------- | ------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `user`   | `prestashop-restore`      | **prestashop-store-rollback** | Restore a PrestaShop store to a previous backup. Use when you need to rollback, for example, if an update fails or if you need to restore a previous state of the store. |
| `user`   | `prestashop-update`       | **prestashop-store-update**   | Update a PrestaShop store by using the Module Update Assistant. Evaluates compatibility and proceeds with the upgrade.                                                   |
| `user`   | `prestashop-update-check` | **prestashop-store-check**    | Check if a PrestaShop store is ready to be updated. Assesses compatibility and available versions without starting the actual update.                                    |

---

_Contributions to add new application domains, user skills, or developer skills
are welcome!_
