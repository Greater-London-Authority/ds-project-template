# ds-package-template

Copier template for data science projects in the City Intelligence Data Science Team.

## Project vs package

A **project** is the full working environment: raw data, notebooks, outputs, configuration, and the Git repository. A **package** is the installable Python library that lives inside the project under `src/`. It contains the reusable logic — data loading, models, plots — that can be imported, tested, and versioned independently.

This template scaffolds both: the project structure around it (notebooks, data, outputs) and the package inside it (the `src/` directory). The project is the container; the package is the artefact that gets distributed and depended on by other projects.

## Prerequisites

### 1. Install pixi

If you don't have pixi installed:

```bash
# macOS / Linux
curl -fsSL https://pixi.sh/install.sh | bash

# macOS via Homebrew
brew install pixi

# Windows (PowerShell)
iwr -useb https://pixi.sh/install.ps1 | iex
```

Restart your terminal after installing, then verify with `pixi --version`.

### 2. Install copier

```bash
pixi global install copier
```

The DST team at the GLA recommends using pixi for all data science projects. `pixi global` installs copier into an isolated environment, keeping it separate from any project dependencies.

## Usage

### Create a new project

```bash
copier copy gh:your-org/ds-package-template path/to/new-project
```

### Update an existing project from the template

```bash
cd path/to/existing-project
copier update
```

## What gets generated

```
project-name/
├── src/
│   └── package_name/
│       ├── __init__.py     # exports __version__ and __all__
│       ├── py.typed        # signals type hints are shipped
│       ├── config.py       # reads env vars via pydantic-settings
│       └── cli.py          # optional, only if has_cli=true
├── tests/
│   ├── __init__.py
│   └── conftest.py
├── notebooks/              # exploratory work — not imported by the package
├── data/
│   └── raw/                # gitignored — immutable source data
├── outputs/                # gitignored — reproducible from code
├── pyproject.toml
├── .env.example            # documents required env vars — committed
├── .gitignore
└── README.md
```

The template generates a minimal starting point. Add `conf/`, `data/interim/`, `data/processed/`, `outputs/figures/` and other folders when the project grows to need them — see the [Data Science Project Structure guide](../housing-projections/docs/data-science-project-structure.md) for guidance on when to add what.

## Template variables

| Variable | Description | Default |
|---|---|---|
| `package_name` | Project / package name (hyphens ok, e.g. `housing-projections`) | — |
| `import_name` | Importable Python name (underscores, auto-derived) | — |
| `description` | One-line description of the project | — |
| `author_name` | Author full name | — |
| `author_email` | Author email address | — |
| `has_cli` | Generate a CLI entry point | `false` |
| `python_version` | Minimum Python version | `3.12` |
| `platforms` | Target pixi platforms | osx-arm64, linux-64, win-64 |

## After scaffolding

```bash
cd project-name
git init
git add .
git commit -m "Initial scaffold from ds-package-template"

pixi install      # creates the environment
pixi run test     # confirms the scaffold works
```
