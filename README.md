# nebi-skill

A [Claude Code skill](https://code.claude.com/docs/en/skills) that makes [Nebi](https://nebi.nebari.dev) + [Pixi](https://pixi.sh) the default environment management system in your development workflow.

Instead of reaching for `pip`, `venv`, `conda`, or `uv`, Claude will automatically use `nebi init`, `pixi add`, and `nebi shell` whenever you're setting up or managing a project environment.

## What is Nebi?

Nebi is multi-user environment management built on top of Pixi — think of it as git for environments. It adds versioning, team sharing, and access control on top of Pixi's fast, cross-platform package management (conda-forge + PyPI + system libraries in a single lockfile).

- **Docs**: https://nebi.nebari.dev
- **GitHub**: https://github.com/nebari-dev/nebi

## Prerequisites

Install Pixi first:

```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

Then install Nebi:

```bash
pixi global install nebi nebi-desktop
```

## Installing the Skill

### Claude Code (global — available in all projects)

```bash
mkdir -p ~/.claude/skills/nebi
curl -fsSL https://raw.githubusercontent.com/<your-username>/nebi-skill/main/nebi/SKILL.md \
  -o ~/.claude/skills/nebi/SKILL.md
```

Or clone the repo and copy:

```bash
git clone https://github.com/<your-username>/nebi-skill.git
cp -r nebi-skill/nebi ~/.claude/skills/nebi
```

### Claude Code (project-local — one project only)

```bash
mkdir -p .claude/skills/nebi
cp nebi/SKILL.md .claude/skills/nebi/SKILL.md
```

### Claude.ai

1. Download `nebi.skill` from the [Releases](../../releases) page (or ZIP the `nebi/` folder yourself)
2. Go to **Customize → Skills → "+" → Create skill**
3. Upload the ZIP file

## What the Skill Does

Once installed, Claude will automatically:

- Use `nebi init` instead of `python -m venv`, `conda create`, or `uv venv` when starting a project
- Use `pixi add <package>` instead of `pip install`
- Use `nebi shell <workspace-name>` to activate environments — by name, from any directory
- Maintain a `pixi.toml` alongside `pyproject.toml` in Python library/app projects, keeping them in sync
- Use `nebi push` / `nebi pull` when sharing or syncing environments across machines or teammates

## Skill Highlights

| Trigger | What Claude does instead |
|---|---|
| "pip install X" | `pixi add X` or `pixi add --pypi X` |
| "create a virtual environment" | `nebi init` |
| "set up the project" | `nebi init` + `pixi add ...` |
| Has a `requirements.txt` | Migrates to `pixi add --pypi` |
| Has a `pyproject.toml` | Creates + maintains a paired `pixi.toml` |
| "share my environment" | `nebi push` or `nebi publish` |
| "activate the environment" | `nebi shell <workspace-name>` |

## Repo Structure

```
nebi-skill/
├── README.md
├── LICENSE
└── nebi/
    └── SKILL.md
```

## Contributing

Issues and PRs welcome. Nebi is in early release, so the skill may need updates as the tool evolves — particularly around `pyproject.toml` support.

## License

MIT — see [LICENSE](LICENSE)
