---
name: chronic
description: >
  Manage `chronic` wrapper for suppressing noisy command output in shell commands.
  Use this skill proactively whenever:
  (1) Running shell commands that produce verbose output on success (builds, linters, formatters, tests, migrations, deploys),
  (2) A command wrapped in chronic is hiding output you or the user actually need to see,
  (3) Reviewing or writing Justfiles, Makefiles, CI scripts, or shell scripts that invoke build/check/lint/test commands.
  Chronic runs a command silently on success but shows full output on failure.
---

# Chronic

`chronic` (from `moreutils`) runs a command quietly unless it fails. On success (exit 0) all stdout/stderr is suppressed. On failure the full output is shown.

## Installation

Ensure `chronic` is available before use:

```bash
# NixOS / nix
nix-shell -p moreutils --run "chronic ..."
# or add moreutils to system/devshell packages

# Debian/Ubuntu
apt install moreutils

# macOS
brew install moreutils
```

If the project uses `flake.nix`, `devbox.json`, or `devenv.nix`, add `moreutils` there.

## When to Add chronic

Wrap a command with `chronic` when **all** of these are true:

1. The command produces output on success that neither you nor the user need to read
2. The command's failure output is sufficient for diagnosing problems
3. The command is invoked repeatedly (in Justfiles, Makefiles, CI, or interactive sessions)

Common candidates: linters, formatters, type checkers, static analysis, compilation, dependency installation, database migrations, deploy scripts.

### Examples

```makefile
# Before — noisy on every run
check:
    eslint src/
    tsc --noEmit

# After — silent on success, verbose on failure
check:
    chronic eslint src/
    chronic tsc --noEmit
```

```just
# Justfile
check:
    chronic cargo clippy --all-targets
    chronic cargo fmt -- --check
```

When running ad-hoc commands in the terminal:

```bash
chronic npm install
chronic terraform plan -detailed-exitcode
```

## When to Remove chronic

Remove `chronic` from a command when **any** of these are true:

1. The output contains information the user needs even on success (e.g., build artifacts paths, deploy URLs, generated file lists, timing/performance data)
2. The command is interactive or streams progress that the user wants to monitor
3. You or the user are debugging — temporarily remove chronic to see full output, then restore it after the issue is resolved. But also consider adding an extra command for that
4. The command's exit code doesn't reliably reflect success/failure (some tools exit 0 on warnings)

**Do not bypass chronic by calling the underlying command directly** (e.g., running `cargo clippy` instead of `just check` to avoid chronic). Instead, remove chronic from the wrapper (Justfile/Makefile) for that specific command.

## Flags

- `chronic -v` — on failure, labels stdout vs stderr and shows the return code. Use when diagnosing which stream contains the error.
- `chronic -e` — triggers output when stderr is non-empty, even if exit code is 0. Use for commands that print warnings to stderr but still exit 0.

## Continuous Optimization

Adopt chronic incrementally and adjust based on experience:

1. **Start without chronic** for new/unfamiliar commands until their output patterns are understood
2. **Add chronic** once a command is confirmed to be noisy-on-success and informative-on-failure
3. **Remove chronic** when output turns out to be useful — don't hesitate, this is expected and healthy
4. **Re-add chronic** after debugging is complete

When you notice a command producing output that nobody reads, suggest adding chronic. When chronic is hiding something useful, suggest removing it. Mention these suggestions naturally, not as a separate concern.
