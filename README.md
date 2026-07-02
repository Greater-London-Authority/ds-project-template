# ds-project-template

Copier template for data science projects in the City Intelligence Data Science Team.

## Project types

**`analysis`** — A standalone analysis (report, model, one-off dataset). Scripts and notebooks only. No `src/` package, no tests directory.

**`project`** — A standalone project with an internal Python package (`src/` layout) and tests. Use this when you want to organise reusable code within a project but do not intend to share it with other teams.

**`package`** — A shared library for use by other projects, teams, or people — whether published to PyPI or used internally (e.g. `gla-data`, `hsds-box`). Includes publishing, versioning, and type-checking tooling.

## Prerequisites

### 1. Install pixi

If you don't have pixi installed:

```bash
# macOS/Linux
curl -fsSL https://pixi.sh/install.sh | sh

# macOS (Homebrew)
brew install pixi

# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm -useb https://pixi.sh/install.ps1 | iex"
```

Check the [latest installation instructions](https://pixi.sh/latest/getting_started/installation/) if these are out of date.

Restart your terminal after installing, then verify with `pixi --version`.

### 2. Install copier

```bash
pixi global install copier
```

The DST team at the GLA recommends using pixi for all data science projects. `pixi global` installs copier into an isolated environment, keeping it separate from any project dependencies.

## Usage

### Create a new project

```bash
copier copy gh:Greater-London-Authority/ds-project-template path/to/new-project
```

### Update an existing project from the template

```bash
cd path/to/existing-project
copier update
```

## What gets generated

The structure varies by project type.

### `analysis`

```
project-name/
├── notebooks/
├── data/
│   └── raw/                # gitignored — immutable source data
├── outputs/                # gitignored — reproducible from code
├── pixi.toml               # environment and dependencies
├── pyproject.toml          # tool configuration (ruff etc.)
├── .copier-answers.yml
├── .env.example
├── .gitignore
└── README.md
```

### `project`

```
project-name/
├── src/
│   └── package_name/
│       ├── __init__.py     # exports __version__ and __all__
│       ├── py.typed        # signals type hints are shipped
│       ├── config.py       # example settings class using env vars
│       └── cli.py          # only present if has_cli=true
├── tests/
│   ├── __init__.py
│   ├── conftest.py         # shared fixtures
│   └── test_placeholder.py # replace with real tests
├── notebooks/
├── data/
│   └── raw/                # gitignored — immutable source data
├── outputs/                # gitignored — reproducible from code
├── pixi.toml               # environment, dependencies, and tasks
├── pyproject.toml          # package metadata, build system, tool config
├── .copier-answers.yml
├── .env.example
├── .gitignore
└── README.md
```

### `package`

```
project-name/
├── src/
│   └── package_name/
│       ├── __init__.py     # exports __version__ and __all__
│       ├── py.typed        # signals type hints are shipped
│       ├── config.py       # example settings class using env vars
│       └── cli.py          # only present if has_cli=true
├── tests/
│   ├── __init__.py
│   ├── conftest.py         # shared fixtures
│   └── test_placeholder.py # replace with real tests
├── pixi.toml               # environment, dependencies, and tasks
├── pyproject.toml          # package metadata, build system, tool config
├── .copier-answers.yml
├── .env.example
├── .gitignore
└── README.md
```

## Template variables

| Variable | Description | Default |
|---|---|---|
| `project_type` | `analysis`, `project`, or `package` — see above | `project` |
| `package_name` | Project / package name (hyphens ok, e.g. `housing-projections`) | — |
| `import_name` | Importable Python name (underscores, auto-derived) | — |
| `description` | One-line description of the project | — |
| `author_name` | Author full name | — |
| `author_email` | Author email address | — |
| `has_cli` | Generate a CLI entry point (`project` and `package` only) | `false` |
| `python_version` | Minimum Python version | `3.12` |
| `platforms` | Target pixi platforms | osx-arm64, linux-64, win-64 |

## After scaffolding

```bash
cd project-name
git init
git add .
git commit -m "Initial scaffold from ds-project-template"

pixi install      # creates the environment
pixi run test     # confirms the scaffold works (project and package types)
```
