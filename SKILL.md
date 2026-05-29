---
name: rpa-project-workflow
description: Complete RPA Python project workflow for initializing a new project from the remote rpa-dev-template, aligning project identity, creating local Git history, preserving the AI development contract, and preparing handoff for follow-up business logic implementation. Use when the user asks to initialize a RPA project, create a project from the development template, pull the remote template before work, prepare an RPA Code project for AI business development, or standardize the project-start workflow.
---

# RPA Project Workflow

## Overview

Use this skill at the start of an RPA Python Code project. It turns the remote `rpa-dev-template` into a local project, aligns the project name, initializes local Git, then prepares the project for AI-led business development using the template's `AGENTS.md` contract.

This skill is a complete standalone workflow for creating a local RPA Python project from the remote development template and preparing it for AI-led business implementation.

## Required Script

Prefer the bundled script for initialization:

```powershell
python "C:\Users\redballoon\Desktop\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "C:\Users\redballoon\Desktop\CodePJ\项目名"
```

Optional arguments:

```powershell
python "C:\Users\redballoon\Desktop\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "D:\CraftPJ\项目名" --template-url "git@github.com:redballoom/rpa-dev-template.git"
python "C:\Users\redballoon\Desktop\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "C:\tmp\项目名" --skip-git
```

The script path is part of the workflow contract. Do not rewrite the initialization logic manually unless the script is missing or must be patched for the local environment.

## Workflow

### 1. Resolve Project Identity

Collect or infer:

- `project_name`: the human-facing project name, Chinese allowed.
- `target_dir`: the final local project directory.
- `template_url`: default `git@github.com:redballoom/rpa-dev-template.git`.

If the user does not specify `target_dir`, use the current working directory only when it is empty or clearly intended as the final project directory. Do not overwrite non-empty directories without explicit approval.

### 2. Clone Remote Template

Clone the remote template into a temporary directory, not directly into the final project directory. If cloning fails, diagnose in this order:

```powershell
git ls-remote git@github.com:redballoom/rpa-dev-template.git
ssh -T git@github.com
```

If network or SSH access is blocked, ask the user to approve the required command or provide a reachable template source.

### 3. Copy and Align

Copy template files into the final target directory while excluding template runtime and history files:

- `.git/`
- `__pycache__/`
- `.pytest_cache/`
- `logs/`
- `crash_snapshots/`
- `.runner.lock`
- `runner_*.json`
- `data/output/` runtime output files

Then align the project identity:

- Replace `开发模板` with `project_name` in text files where appropriate.
- Replace `rpa-dev-template` with `project_name` in README or project-facing docs.
- Set `run.bat` `PROJECT` to `project_name`.
- Ensure `project.json` exists. If missing, copy from `project.template.json`.
- Clear real secrets in `project.json`: Feishu webhook, Linear API key, AI API key, tokens, app secrets.

### 4. Preserve AI Handoff Contract

After copying, verify the project contains:

- `AGENTS.md`
- `README.md`
- `runner.py`
- `run.bat`
- `core/entry.py`
- `docs/SHADOWBOT_INPUT_CONTRACT.md`
- `docs/RPA_PYTHON_BOUNDARY.md`
- `docs/examples/`
- `tests/`

If `AGENTS.md` is missing, create or copy it before handing the project to another AI. Future agents must use it to design `tasks[].type`, `payload`, handlers, tests, and docs.

### 5. Initialize Local Git

Initialize a fresh local repository in the target project, not the template repository history:

```powershell
git init
git add -A
git commit -m "init: 项目名"
```

Do not configure a remote or push unless the user explicitly asks.

### 6. Optional Validation

Do not install dependencies unless requested. If dependencies are already available, run:

```powershell
python -m pytest tests/ -v
```

If tests cannot run, report why and list the exact command the user can run later.

### 7. Handoff for Business Logic

At the end, report:

- Project path.
- Initial commit hash, if Git was initialized.
- Whether `AGENTS.md` exists.
- Whether `input.json` examples exist.
- Next command for business implementation agents to inspect the project.

The next AI business-development step should follow the generated project's `AGENTS.md`: design or confirm `input.json`, implement handlers for `tasks[].type`, write output to `data/output/`, and add tests.

## Do Not Do

- Do not implement business logic during initialization.
- Do not keep the template `.git` history.
- Do not commit real secrets.
- Do not push to GitHub unless explicitly requested.
- Do not silently overwrite non-empty target directories.
- Do not remove `AGENTS.md`, docs, or tests to make the project look smaller.

## Failure Handling

- Target directory non-empty: stop and ask whether to use another path or allow overwrite.
- Clone failed: check `git ls-remote`, then SSH auth.
- `project.json` missing: copy `project.template.json`, then set project name and clear secrets.
- Git commit failed because user identity is missing: leave files initialized and report `git config user.name` / `git config user.email` commands.
- Tests fail after initialization: do not hide failures; report the failing tests and leave the project ready for repair.


