# ds-project-template

Copier template for data science projects in the City Intelligence Data Science Team.

## Project types

**`analysis`** — A standalone analysis (report, model, one-off dataset). Scripts and notebooks only. No `src/` package, no tests directory.

**`project`** — A standalone project with an internal Python package (`src/` layout), tests, and an `experiments/` folder for fitting, trials and checks. Use this when you want to organise reusable code within a project but do not intend to share it with other teams.

**`package`** — A shared library for use by other projects, teams, or people — whether published to PyPI or used internally (e.g. `gla-data`, `hsds-box`). Includes publishing, versioning, and type-checking tooling.

### Moving between types

Work often starts as exploration and becomes something that is run again. Start as an `analysis`; when code starts being reused between notebooks, turn it into a `project` (see [Turn an analysis into a project](#turn-an-analysis-into-a-project)). Data access that more than one project needs belongs in a `package` (as `gla-data` did), not copied between projects.

In a `project`:

- `src/<package>/` holds the code the project's outputs depend on. Code used both to fit a model and to apply it (features, corrections) lives here, so the two cannot drift apart.
- `experiments/` holds code run to find something out. It imports the package; the package never imports it. When a result is accepted, the code it needs moves into the package, with tests, and options only the experiments used are dropped.
- Settings and paths are defined once, in `config.py`, and resolved against the project root.
- Decisions go in `docs/decisions/`, open work in `docs/work-log.md`.

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

Then set up the repo and install the environment:

```bash
cd path/to/new-project
git init
git add .
git commit -m "Initial scaffold from ds-project-template"
pixi install
pixi run test     # project and package types only
```

On Windows, keep the project path short (e.g. `C:\Users\<you>\dev\<project>`). Environments nest deeply, and paths over 260 characters make `pixi install` fail or leave files out.

### Update an existing project from the template

```bash
cd path/to/existing-project
copier update
```

Commit your work first. Where you have changed a file the template also changed, copier writes both versions into the file with conflict markers for you to resolve.

### Turn an analysis into a project

```bash
cd path/to/analysis
copier update --data project_type=project
```

This adds `src/`, `tests/`, `experiments/`, `docs/decisions/` and the CI workflow, and keeps your notebooks. Add `--data has_cli=true` for a command line.

### Updating from v0.1.0

- `pyproject.toml` and `src/<package>/config.py` will conflict if you edited them. Keep your dependencies and settings, in the new layout.
- `config.py` no longer creates `settings` when it is imported. Replace `from <package>.config import settings` with `get_settings()`, called where a value is needed.
- Relative paths in settings, and `.env`, are now resolved against the project root (`<PREFIX>_ROOT`, else the pixi project folder), not the folder a command is run from. Code run outside `pixi run` (e.g. a notebook kernel started directly from `.pixi/envs`) needs `<PREFIX>_ROOT` set.
- An unknown `<PREFIX>_` key in `.env` is now an error.
- The version now comes from git tags, not `pyproject.toml`. Packages: release with `git tag vX.Y.Z` instead of `pixi run release-*`.
- The CLI entry point is now `cli:app`; `--help` and options did not work before.

## Versions

Projects and packages take their version from the latest git tag (`git tag v0.3.0`). Between tags the version includes the commit (`0.3.1.dev2+g1a2b3c4`).

The version is worked out when the package is installed, and pixi installs the package in editable mode, so `__version__` does not change when you commit or tag: it stays at whatever it was at the last install. Run `pixi reinstall <package-name>` after tagging. If outputs need to record the exact commit that made them, read it from git when the run starts (`git rev-parse HEAD`, and `git status --porcelain` for uncommitted changes) rather than relying on `__version__`.

## What gets generated

The structure varies by project type. Every type also gets `docs/work-log.md`, and `CLAUDE.md` if `has_claude_md` is chosen.

### `analysis`

```
project-name/
├── notebooks/
├── data/                   # gitignored
├── outputs/                # gitignored — reproducible from code
├── docs/
│   └── work-log.md         # open work, one line each
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
│       ├── config.py       # settings and paths, resolved against the project root
│       └── cli.py          # only present if has_cli=true
├── tests/
│   ├── __init__.py
│   ├── conftest.py         # shared fixtures
│   ├── test_config.py      # settings behaviour
│   └── test_placeholder.py # replace with real tests
├── experiments/            # fitting, trials, checks; imports the package
├── notebooks/
├── data/                   # gitignored
├── outputs/                # gitignored — reproducible from code
├── docs/
│   ├── decisions/          # one short file per decision
│   └── work-log.md
├── .github/workflows/      # lint and tests on pull requests and pushes to main
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
│       ├── config.py       # settings and paths, resolved against the project root
│       └── cli.py          # only present if has_cli=true
├── tests/
│   ├── __init__.py
│   ├── conftest.py         # shared fixtures
│   ├── test_config.py      # settings behaviour
│   └── test_placeholder.py # replace with real tests
├── docs/
│   ├── decisions/
│   └── work-log.md
├── .github/workflows/      # lint, tests, type checks on PRs and pushes to main
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
| `has_cli` | Generate a Typer CLI entry point (`project` and `package` only) | `false` |
| `uses_gla_data` | Add `gla-data` as a dependency | `false` |
| `has_claude_md` | Add a `CLAUDE.md` for Claude Code | `false` |
| `python_version` | Minimum Python version | `3.12` |
| `platforms` | Target pixi platforms | osx-arm64, linux-64, win-64 |

## Testing the template

`.github/workflows/test-template.yml` generates each project type on Linux and Windows, with two sets of options (CLI and CLAUDE.md without gla-data; gla-data without CLI or CLAUDE.md). It checks that no template text is left unrendered, runs the generated project's own lint, tests, type checks and `--help`, imports gla-data where chosen, and installs the package outside pixi to check its declared dependencies.
