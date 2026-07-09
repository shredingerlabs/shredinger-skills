---
name: verification-python
description: Defines what "verified" means for general-purpose Python software (scripts, CLIs, libraries, data pipelines, backends) that is NOT primarily a web frontend concern. Use this whenever a change touches Python source, a package's public API, a CLI entry point, or a script — covers typecheck/lint/test requirements, environment assumptions, and banning silent exception swallowing. If the project is a web backend, also consult verification-web for the API-boundary rules; if it's ROS2, use verification-ros2 instead.
---

# Python Verification (general, non-web)

Success = typecheck (mypy/pyright) + lint (ruff/flake8) + test suite (pytest) all green, **and**
an actual run of the real entry point (script, CLI, or a real import of the library). A green
test suite is not proof the thing runs end to end in its actual usage context.

## Requirements

- State environment assumptions explicitly before implementing: Python version, dependency
  manager (poetry/uv/pip/conda), OS-specific behavior, and anything assumed to already be
  installed.
- No silent exception swallowing for the sake of a shorter diff — a bare `except:` or
  `except Exception: pass` is a bug, not a simplification.
- If the change touches a package's public API, confirm it via actual usage of that API, not only
  internal unit tests that call private functions directly.
