---
name: rpa-project-workflow
description: Initialize a new RPA Python project from the remote rpa-dev-template and prepare it for AI-led business implementation. Use when the user asks to create, scaffold, clone, initialize, reset, or prepare an RPA/Python automation project from the template, including setting the project name, choosing a target directory, clearing template secrets, initializing a fresh Git repository, validating AGENTS.md/docs/tests handoff files, or producing the next prompt for business-logic implementation.
---


# RPA Project Workflow

Use this skill at the start of an RPA Python automation project. The goal is to turn `rpa-dev-template` into a clean local project, align project identity, initialize local Git, and leave future agents with the template's `AGENTS.md` handoff contract.

## Primary Tool

Use the bundled initializer instead of rewriting the clone/copy/scrub/git logic:

```powershell
python "C:\Users\redballoon\.agents\skills\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "C:\Users\redballoon\Desktop\CodePJ\项目名"
```

Optional arguments:

```powershell
python "C:\Users\redballoon\.agents\skills\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "D:\CraftPJ\项目名" --template-url "git@github.com:redballoom/rpa-dev-template.git"
python "C:\Users\redballoon\.agents\skills\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "C:\tmp\项目名" --skip-git
python "C:\Users\redballoon\.agents\skills\rpa-project-workflow\scripts\init_rpa_project.py" --name "项目名" --target "C:\Users\redballoon\Desktop\CodePJ\项目名" --force-overwrite
```

Patch the script only when local environment behavior proves it wrong.

## Workflow

1. Resolve inputs:
   - `project_name`: human-facing name; Chinese is allowed.
   - `target_dir`: final local project directory.
   - `template_url`: default `git@github.com:redballoom/rpa-dev-template.git`.
2. Refuse to overwrite a non-empty target directory unless the user explicitly approves that exact path. When approval is explicit, pass `--force-overwrite` to the initializer instead of manually copying around it.
3. Run the initializer. It clones the template to a temporary directory, copies files, replaces template identity, updates `run.bat`, prepares `project.json`, clears likely secret fields, validates handoff files, and optionally initializes Git.
4. Read the script's final JSON result and report the project path, missing handoff files, and initial commit hash when present.
5. Do not implement business logic during initialization. The next step belongs to the generated project's `AGENTS.md`.

If the user does not specify `target_dir`, use the current working directory only when it is empty or clearly intended as the final project directory. Otherwise choose `C:\Users\redballoon\Desktop\CodePJ\<project_name>` when that path is missing or empty.

## Failure Handling

Target directory non-empty:
- Stop and ask whether to use another path or allow overwrite of that exact directory. If the user allows overwrite, rerun the initializer with `--force-overwrite`.

Clone failure:
- Diagnose with:

```powershell
git ls-remote git@github.com:redballoom/rpa-dev-template.git
ssh -T git@github.com
```

If network or SSH access is blocked, request the needed approval or ask the user for another reachable template URL.

Git commit failure:
- If the error is missing Git identity, leave the project initialized and report the needed `git config user.name` and `git config user.email` commands.

Missing handoff files:
- Report them plainly. Do not delete docs/tests to make the project look smaller.

## Optional Validation

Do not install dependencies unless requested. If dependencies are already available in the generated project, run:

```powershell
python -m pytest tests/ -v
```

If tests cannot run, report why and list the exact command the user can run later.

For detailed post-initialization checks, read `references/workflow-checklist.md`.

## Final Report

Include:

- Project path.
- Initial commit hash, if Git was initialized.
- Whether `AGENTS.md` exists.
- Whether `docs/examples/` exists.
- Any missing handoff files or test failures.
- The next useful prompt or command for business implementation.

Suggested next prompt:

```text
阅读这个 RPA Python 项目的 AGENTS.md、README.md 和 docs/ 输入契约。根据我的业务目标设计 input.json 的 tasks[].type 和 payload，然后实现对应 handler、示例和测试。
```

## Do Not Do

- Do not implement business logic during initialization.
- Do not keep the template `.git` history.
- Do not commit real secrets.
- Do not push to GitHub unless explicitly requested.
- Do not silently overwrite non-empty target directories.
- Do not remove `AGENTS.md`, docs, or tests to make the project look smaller.

