# Project Guidelines

This repository is a data science / machine learning capstone project.

The main goals are to:

- keep the project clean and easy to understand
- keep analyses reproducible
- use consistent project tooling
- keep notebooks organized and readable
- separate exploratory work from reusable project code
- avoid unnecessary complexity or overengineering

When making changes, prefer simple, clear solutions over clever or overly abstract ones.

---

## Core Project Tooling

This project uses:

- **Git** for source code, notebooks, configuration, and project history
- **DVC** for datasets and large data artifacts
- **uv** for Python environments and dependency management

These tools should be used consistently throughout the project.

Do not introduce alternative tooling unless explicitly requested.

---

## Python Environment and Dependency Management

This project uses `uv` as the single Python package and environment manager.

Always use `uv` for Python dependency and environment management unless explicitly instructed otherwise.

### Adding dependencies

Use:

```bash
uv add <package>
```

For development-only dependencies:

```bash
uv add --dev <package>
```

### Removing dependencies

Use:

```bash
uv remove <package>
```

### Synchronizing the environment

Use:

```bash
uv sync
```

### Running project commands

Prefer running project commands through the uv-managed environment:

```bash
uv run python ...
uv run pytest
uv run ruff check .
uv run jupyter lab
```

Do not:

- use `pip install` directly
- create separate environments with `python -m venv`
- use Conda unless explicitly requested
- maintain a separate `requirements.txt` unless explicitly requested
- manually edit `uv.lock`
- install project dependencies globally
- rely on packages that are installed locally but not declared in the project

Treat:

```text
pyproject.toml
uv.lock
```

as the source of truth for the Python environment.

If code or a notebook requires a package, add it through `uv`.

### Installing Missing Dependencies

If a task requires a Python dependency that is not currently installed or declared in the project, you may install it as needed.

Use `uv` for all dependency installation.

For runtime dependencies:

```bash
uv add <package>
```

For development-only tools:

```bash
uv add --dev <package>
```

You do not need to stop and ask for permission to install a normal, well-established dependency when it is clearly required to complete the requested task.

Before adding a dependency:

- first check whether the required functionality is already available through the Python standard library or an existing project dependency
- prefer widely used, actively maintained packages
- avoid adding a package for something trivial that can be implemented clearly with existing tools
- avoid adding overlapping libraries that serve the same purpose without a clear reason

When a dependency is added, keep `pyproject.toml` and `uv.lock` updated through `uv`.

Do not use `pip install`, global installations, Conda, or ad-hoc virtual environments.

If installing a dependency would introduce a major framework, significantly change the project architecture, require system-level software, or have substantial consequences for the project, ask before proceeding.

---

## Data Management with DVC

Project datasets and large data artifacts should be managed with DVC.

Use DVC rather than Git for large or frequently changing data.

Typical workflow:

```bash
dvc add data/
dvc push
dvc pull
```

Use the configured DVC remote for shared project data.

Do not:

- commit DVC-managed datasets directly to Git
- move DVC-managed data back into normal Git tracking
- duplicate large datasets unnecessarily
- delete or overwrite raw source data without a clear reason
- commit large generated artifacts to Git if they belong in DVC

Git should track DVC metadata such as:

```text
*.dvc
.dvc/config
dvc.yaml
dvc.lock
```

when applicable.

Whenever new large files are created, consider whether they should be:

1. tracked by Git
2. tracked by DVC
3. ignored entirely

Prefer preserving raw datasets and creating derived or processed versions separately.

---

## General Coding Style

Write code that is:

- clear
- readable
- reasonably concise
- easy to understand
- modular where reuse is likely

Avoid:

- deeply nested logic
- giant functions
- unnecessary classes
- premature abstraction
- duplicated logic
- unnecessary configuration layers
- clever code that makes the project harder to understand

Prefer descriptive names for:

- variables
- functions
- files
- modules
- DataFrame columns

Functions should generally perform one logical task.

Use type hints when they improve clarity, especially for reusable functions, but do not add excessive typing to simple analysis code.

Use docstrings for reusable functions when their purpose, inputs, outputs, or assumptions are not immediately obvious.

Do not add comments that merely restate code.

Comments should explain:

- reasoning
- assumptions
- unusual behavior
- important implementation decisions

---

## Clarifying Questions and Ambiguity

When a task is ambiguous in a way that could materially change the implementation, ask a clarifying question before proceeding.

Good reasons to ask include:

- multiple reasonable interpretations of the requested behavior
- uncertainty about expected inputs or outputs
- uncertainty about where new functionality should live
- choices that would meaningfully affect project structure
- choices between substantially different implementation approaches
- assumptions that could affect analysis correctness
- uncertainty about whether existing behavior should be preserved or changed
- uncertainty about what the user intends an exploratory analysis to answer

When asking a clarifying question, make it easy to answer.

Prefer:

- a concise explanation of what is unclear
- 2–4 reasonable options when appropriate
- a brief note about the tradeoff between those options
- an option for the user to provide a different answer

For example:

```text
There are two reasonable ways I can structure this:

1. Put the cleaning logic in the notebook — simpler if this is a one-off analysis.
2. Put it in `src/` and import it — better if multiple notebooks will use it.

Which do you prefer? I can also use a different structure if you have one in mind.
```

Do not ask questions unnecessarily.

If the task is clear and the remaining choices are minor implementation details, use reasonable judgment and proceed.

Examples of things that usually do **not** require clarification:

- variable names
- small formatting decisions
- straightforward function decomposition
- obvious file placement based on existing project conventions
- routine dependency installation
- minor refactors that preserve behavior

If a reasonable default is low-risk and easy to change later, prefer proceeding with that default rather than interrupting the workflow.

If an assumption is necessary but does not justify stopping the task, state the assumption briefly and continue.

For large or consequential changes, prefer clarifying the intended behavior before making broad modifications.

---

## Project Structure

The repository structure is still evolving.

Do not reorganize large parts of the repository or introduce a complicated architecture unless there is a clear reason.

For now, prefer a lightweight structure similar to:

```text
.
├── data/
├── notebooks/
│   ├── exploratory/
│   └── production/
├── src/
├── tests/
├── AGENTS.md
├── README.md
├── pyproject.toml
└── uv.lock
```

Additional directories may be added when they become useful.

Do not create folders preemptively just because they are common in other projects.

Use:

- `data/` for project datasets managed through DVC
- `notebooks/exploratory/` for investigation and experimentation
- `notebooks/production/` for polished and reproducible analyses
- `src/` for reusable Python code
- `tests/` for tests of reusable functionality

The structure may evolve as the project becomes more defined.

---

## Notebook Philosophy

Notebooks are an important part of this project.

Treat notebooks as real project artifacts, not disposable scratch files.

There are two notebook categories:

```text
notebooks/
├── exploratory/
└── production/
```

---

## Exploratory Notebooks

Exploratory notebooks are used for:

- understanding datasets
- testing ideas
- visual exploration
- prototyping transformations
- experimenting with models
- investigating research questions

Exploratory notebooks may contain experimentation, but they should still be reasonably clean and readable.

Do not allow exploratory notebooks to become:

- enormous
- chaotic
- full of abandoned cells
- difficult to follow

If an exploratory notebook starts accumulating substantial reusable code, move that code into `src/`.

Exploration can be flexible, but it should still be organized.

---

## Production Notebooks

Production notebooks should be:

- clean
- concise
- reproducible
- focused
- easy to read from top to bottom
- appropriate to show to another student, collaborator, instructor, or reviewer

Production notebooks should not contain:

- abandoned experiments
- debugging cells
- unused code
- repeated analysis
- excessive output
- large amounts of temporary experimentation

If exploratory work becomes part of the final workflow, create or clean up an appropriate production notebook.

---

## Notebook Structure

Every substantial notebook should begin with a clear Markdown introduction.

A useful default structure is:

```markdown
# Notebook Title

## Purpose

Briefly explain what this notebook is trying to accomplish.

## Data

Describe the main datasets used.

## Questions / Goals

- Goal 1
- Goal 2
- Goal 3
```

Do not force all of these sections into very small notebooks if they are unnecessary.

Organize the body of the notebook using meaningful Markdown headings.

A typical notebook may look like:

```text
# Title

## Purpose

## Setup

## Load Data

## Data Overview

## Analysis

## Results

## Conclusions
```

Use only the sections that improve readability.

---

## Notebook Cleanliness

When creating or editing notebooks:

- keep cells reasonably small
- keep each cell focused on one task
- use Markdown to explain important steps
- place explanation before major analyses
- avoid giant code cells
- avoid giant Markdown walls of text
- remove debugging cells when finished
- remove duplicate analysis
- remove obsolete experiments
- keep imports together near the top
- avoid repeated imports throughout the notebook
- avoid displaying huge DataFrames
- avoid unnecessary console output
- show only the data needed to understand the result

Prefer:

```python
df.head()
```

or concise summaries rather than printing entire datasets.

Notebooks should be easy to scan visually.

---

## Notebook Length

Prefer multiple focused notebooks over one extremely long notebook.

A notebook should generally answer:

- one coherent analysis question
- one logical stage of the project
- one clearly defined exploratory objective

If a notebook becomes difficult to navigate:

1. remove obsolete work
2. extract reusable logic into `src/`
3. separate unrelated analyses into different notebooks

Do not split notebooks solely to satisfy an arbitrary size limit.

---

## Source Code vs Notebooks

Notebooks should communicate the analysis.

Reusable implementation details should generally live in `src/`.

Move logic into `src/` when it is:

- used by multiple notebooks
- reused in multiple workflows
- large enough to distract from the analysis
- important enough to test independently

Examples include:

- shared data loading
- repeated data cleaning
- feature construction
- modeling utilities
- evaluation functions
- shared visualization helpers
- parsing utilities

Then import those functions into notebooks.

Do not move every small notebook operation into `src/`.

Small notebook-specific transformations can remain in the notebook.

The goal is readability, not abstraction for its own sake.

---

## Reproducibility

Production notebooks should ideally run from top to bottom in a fresh environment.

Do not rely on variables created by running cells out of order.

Use deterministic random seeds when randomness affects results that should be reproducible.

Avoid hard-coded absolute paths such as:

```text
/home/user/project/...
C:\Users\...
```

Prefer project-relative paths.

Do not embed:

- API keys
- passwords
- access tokens
- secrets
- private credentials

in code, notebooks, or committed files.

---

## DataFrames and Data Processing

When working with tabular data:

- prefer readable transformations
- use descriptive column names
- avoid surprising in-place mutation
- document important assumptions
- validate important transformations

For joins and merges, explicitly consider:

- join keys
- expected row counts
- duplicated keys
- unmatched rows
- missing values introduced by the merge

Do not silently drop substantial amounts of data without investigating why.

When filtering data significantly, consider reporting how many rows were removed.

---

## Machine Learning

For ML work:

- establish simple baselines before complex models
- clearly separate training and evaluation data
- avoid data leakage
- make preprocessing reproducible
- use consistent evaluation metrics
- record important model settings
- use deterministic seeds when useful
- keep evaluation procedures consistent across models

Do not introduce complex modeling pipelines unless the problem requires them.

Prefer understandable models and workflows when they provide comparable value.

---

## Visualization

Plots should be easy to interpret.

Include meaningful:

- titles
- axis labels
- units
- legends when needed

Avoid unnecessary visual decoration.

Choose visualizations based on the question being answered rather than aesthetics alone.

Do not generate excessive plots when a smaller number communicates the result clearly.

---

## Testing

Reusable code in `src/` should be tested when errors could materially affect the project.

Prioritize tests for:

- data transformations
- parsing logic
- feature construction
- important calculations
- reusable utility functions
- modeling utilities where correctness matters

Do not write tests for trivial notebook presentation code simply to increase coverage.

Before considering a substantial source-code change complete, run relevant tests when they exist.

Prefer:

```bash
uv run pytest
```

---

## Code Quality Tools

If formatting or linting tools are configured in the project, use them consistently.

Prefer running them through `uv`, for example:

```bash
uv run ruff check .
uv run ruff format .
```

Fix meaningful lint issues rather than suppressing them unnecessarily.

Do not introduce formatting tools that conflict with the project's existing tooling.

---

## Git

Git is used for project history, code, notebooks, configuration, documentation, and DVC metadata.

Keep commits logically focused.

Do not commit:

- secrets
- credentials
- `.env` files containing secrets
- virtual environments
- notebook checkpoint directories
- temporary files
- large datasets that belong in DVC
- generated junk files

Do not perform destructive Git operations unless explicitly requested.

Do not rewrite Git history unless explicitly requested.

Before making broad changes, inspect the existing repository and preserve conventions already in use.

---

## Git and DVC Responsibilities

Use Git for:

```text
source code
notebooks
README files
configuration
tests
pyproject.toml
uv.lock
DVC metadata
```

Use DVC for:

```text
raw datasets
processed datasets when large
large intermediate artifacts
large generated data products
```

Do not blur these responsibilities without a clear reason.

---

## Making Changes

Before implementing substantial changes:

1. inspect the relevant files
2. understand the current structure
3. identify the simplest reasonable approach
4. avoid modifying unrelated files

For small changes, implement them directly.

For larger changes, preserve the existing architecture unless there is a clear reason to modify it.

Do not expand the scope of a task unnecessarily.

If a separate issue is discovered that is unrelated to the requested task, mention it rather than automatically refactoring it.

---

## Documentation

Keep `README.md` useful as the project evolves.

Project-wide documentation should eventually explain:

- project purpose
- environment setup
- dependency setup
- data setup
- DVC usage
- repository structure
- how to run important analyses

Do not duplicate large amounts of documentation across multiple files.

When project setup changes significantly, update the README when appropriate.

---

## General Principles

Optimize for a repository that another data scientist could clone, inspect, and understand without requiring extensive explanation from the original author.

Favor:

**clarity over cleverness**

**reproducibility over convenience**

**uv over ad-hoc Python environment management**

**DVC over committing large datasets to Git**

**focused notebooks over giant notebooks**

**reusable source code over duplicated notebook logic**

**simple structure over unnecessary abstraction**

This is a research and data science project, not a large production software system.

Use good engineering practices, but keep the project pragmatic and lightweight.