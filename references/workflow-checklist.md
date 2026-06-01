# RPA Project Workflow Checklist

Use this reference when validating a project initialized by the skill.

## Initialization Inputs

- Project name is non-empty.
- Target directory is explicit or current working directory is intentionally used.
- Target directory is missing or empty, or the user explicitly approved overwrite and the initializer was run with `--force-overwrite`.
- Template URL defaults to `git@github.com:redballoom/rpa-dev-template.git` unless user specifies another source.

## Files That Must Exist After Initialization

- `AGENTS.md`
- `README.md`
- `run.bat`
- `runner.py`
- `project.template.json`
- `project.json`
- `core/entry.py`
- `core/exceptions.py`
- `docs/SHADOWBOT_INPUT_CONTRACT.md`
- `docs/RPA_PYTHON_BOUNDARY.md`
- `docs/examples/`
- `tests/`

## Identity Alignment

- `run.bat` contains `set PROJECT={project_name}`.
- `project.json.project` equals project name.
- `project.json.linear.project_name` equals project name when the field exists.
- `README.md` no longer presents the project as `rpa-dev-template`.
- Real secrets are blank in `project.json`.

## Handoff Contract

- `AGENTS.md` tells future AI agents to design `tasks[].type` and `payload` before implementing business logic.
- Unknown or missing `tasks[].type` must not fake success.
- Business logic changes belong in Python handlers and tests, not in ShadowBot UI flow by default.

## Git

- The template `.git` history is not copied.
- A fresh Git repository exists in the target directory unless `--skip-git` was used.
- The first commit captures the initialized project state.
- When initializing over a non-empty approved target, the commit is created in the actual target directory, not in a temporary workaround directory.

## Next Step Prompt

After initialization, use a prompt like:

```text
阅读这个 RPA Python 项目的 AGENTS.md、README.md 和 docs/ 输入契约。根据我的业务目标设计 input.json 的 tasks[].type 和 payload，然后实现对应 handler、示例和测试。
```

