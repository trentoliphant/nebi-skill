---
name: nebi
description: >
  Environment and workspace management using Nebi (nebi.nebari.dev) and Pixi. Use this skill
  when the user wants a Nebi/Pixi workflow for a Python or data science project: creating or
  tracking a Pixi workspace, adding packages, activating tracked workspaces by name or path,
  sharing environments, importing/publishing environments, or troubleshooting Nebi workspace
  issues. Prefer Nebi + Pixi over ad hoc pip/venv guidance unless the user explicitly asks for
  another tool or the repository is clearly using a different environment manager.
---

# Nebi Skill

Nebi is a multi-user environment management tool built on top of [Pixi](https://pixi.sh).
Think of it as **git for environments**: you can push, pull, diff, and roll back, with access
control built in.

**Docs**: https://nebi.nebari.dev  
**GitHub**: https://github.com/nebari-dev/nebi

---

## Core Principle

**Nebi + Pixi is the preferred workflow when Nebi is in use.** Prefer `nebi` + `pixi` commands
over `python -m venv` or one-off `pip install` steps unless the user explicitly asks for a
different tool or the project is clearly standardized on something else.

---

## Quick Reference: What to Use When

| Situation | Command |
|---|---|
| Starting a new project | `nebi init` |
| Adding a conda/system package | `pixi add <package>` |
| Adding a PyPI-only package | `pixi add --pypi <package>` |
| Activating a tracked workspace by name or path | `nebi shell <workspace>` |
| Running a Pixi task from a tracked workspace | `nebi run <workspace> <task>` |
| Listing tracked workspaces | `nebi workspace list` |
| Pushing workspace to team server | `nebi push <workspace-name>` |
| Pulling a workspace from server | `nebi pull <workspace-name>` |
| Comparing environment versions | `nebi diff <ref-a> <ref-b>` |
| Importing from OCI registry | `nebi import <oci-ref> -o ./my-project` |
| Checking sync status | `nebi status` |

---

## Installation (if not yet installed)

Pixi must be installed first:
```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

Install Nebi (recommended — via pixi global):
```bash
pixi global install nebi nebi-desktop
```

Or via install script (macOS/Linux):
```bash
curl -fsSL https://nebi.nebari.dev/install.sh | sh -s --desktop
export PATH="$HOME/.local/bin:$PATH"
```

Or via conda:
```bash
conda install conda-forge::nebi
conda install conda-forge::nebi-desktop
```

---

## Standard New Project Workflow

```bash
# 1. Create and initialize a new workspace
mkdir my-project && cd my-project
nebi init
# (runs `pixi init` automatically if no pixi.toml exists)

# 2. Add packages — conda-forge packages (including system libs like GDAL, CUDA)
pixi add numpy pandas matplotlib

# 3. Add PyPI-only packages
pixi add --pypi flask

# 4. Activate the environment
pixi shell
# or activate the tracked workspace by name from another directory:
nebi shell my-project

# 5. Run a command or task
pixi run python my_script.py
nebi run my-project jupyter-lab
```

---

## Working with Existing Projects

```bash
# Track an existing Pixi project with Nebi
cd existing-pixi-project
nebi init

# Install dependencies from pixi.toml + pixi.lock
pixi install
```

> **Note**: Nebi currently only supports `pixi.toml` manifests. Projects using
> `pyproject.toml` with `[tool.pixi.*]` tables are not yet supported.

---

## Projects That Also Use `pyproject.toml`

Official Nebi docs currently document `pixi.toml` as the supported manifest. If a repository also
has a `pyproject.toml`, keep Nebi-specific environment management in `pixi.toml` and avoid
claiming that Nebi supports Pixi configuration embedded under `[tool.pixi.*]` in `pyproject.toml`.

Practical guidance:

- Keep runtime package metadata for published Python packages in `pyproject.toml`
- Keep the Nebi/Pixi workspace definition, lockfile, and Pixi tasks in `pixi.toml`
- Keep overlapping dependency and Python-version constraints aligned manually when both files are used
- If the project has no `pixi.toml`, run `nebi init` in the repository root to create one

---

## Activating Workspaces from Anywhere

A key Nebi advantage: activate any tracked workspace by name or path regardless of current
directory.

```bash
# Works from any directory
nebi shell geo-ml
nebi shell data-science -e cuda   # activate a specific pixi environment

# Run a task from a tracked workspace without cd-ing into it
nebi run ml-pipeline train -- --epochs 100
```

---

## Versioning and Sharing

```bash
# Push a versioned snapshot to a Nebi server
nebi push my-project:v1.0

# Pull a workspace (latest or tagged)
nebi pull my-project
nebi pull my-project:v1.0

# Compare two versions
nebi diff my-project:v1.0 my-project:v2.0

# Publish to OCI registry (e.g., quay.io) for distribution
nebi publish my-project --tag v1.0.0

# Import from an OCI registry
nebi import quay.io/myorg/data-science:v1.0 -o ./my-project
```

---

## Removing / Pruning Workspaces

```bash
# Stop tracking (files are NOT deleted)
nebi workspace remove my-project
nebi workspace remove /home/user/old-project

# Remove from server too
nebi workspace remove my-project --remote

# Remove all workspaces whose directories no longer exist
nebi workspace prune
```

---

## Pixi Cheat Sheet (used inside Nebi workspaces)

```bash
pixi add numpy                    # conda-forge package
pixi add "python=3.12.*"          # pin Python version
pixi add --pypi scikit-learn      # PyPI package
pixi add --pypi "flask[async]==3.1.0"
pixi remove numpy                 # remove a package
pixi list                         # show installed packages
pixi install                      # install from existing pixi.lock
pixi shell                        # activate shell
pixi run <task>                   # run a defined task
pixi task add train "python src/train.py"  # define a task
```

---

## Guidance for Common Scenarios

**User says "pip install X"** → Use `pixi add X` (conda-forge) or `pixi add --pypi X`  
**User says "create a virtual environment"** → Use `nebi init` in the project directory when the user wants a Nebi/Pixi workspace  
**User has a `requirements.txt`** → Migrate: `cat requirements.txt | xargs pixi add --pypi`  
**User needs system libraries (GDAL, CUDA, etc.)** → `pixi add gdal` — no apt/brew needed  
**User wants to share their environment** → `nebi push <name>` or `nebi publish`  
**User asks "which environment am I in"** → `nebi workspace list` + `nebi status`  
**User gets dependency conflicts** → Prefer conda-forge packages over PyPI where possible  
**User needs to push to a Nebi server** → Make sure they have logged in first with `nebi login <server-url>`

---

## Links

- Full docs: https://nebi.nebari.dev/docs/introduction
- CLI reference: https://nebi.nebari.dev/docs/cli-reference
- Pixi docs: https://pixi.sh
- GitHub issues: https://github.com/nebari-dev/nebi/issues

> ⚠️ Nebi is in early release. Check GitHub issues if something doesn't work as expected.
