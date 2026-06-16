---
name: debugging-ci-runs
description: Use when a GitHub Actions / CI run fails or partially fails and you need to find the root cause from the logs and fix it — inspecting run/job/step status with gh, filtering noisy logs down to the real error, classifying the failure, fixing iteratively, and re-triggering. Covers the model2owl transform/diff/pages workflows and the rule to pull CI-committed artefacts before editing.
---

# Debugging CI runs

## Overview

CI debugging is a tight loop, not a one-shot read: **inspect → isolate the real
error → classify it → verify against source → minimal fix → push → watch.**
Fixing one failure usually reveals the next, so expect several iterations.

Logs are extremely noisy (deprecation notices, repeated XSLT "imported more than
once" warnings). The skill is mostly about *filtering down to the one line that
matters* and not changing more than that line implies.

## CRITICAL: pull before you touch the branch

On a (partially) successful run, the workflows **commit generated artefacts back
to the branch** (e.g. `chore(ci): transform 2 module(s) #NN [skip ci]`,
`chore(ci): diff …`). Your local branch is now behind.

**Always `git pull --ff-only` (or fetch + rebase) before editing, committing, or
rebasing.** Otherwise your push is rejected as non-fast-forward, or you create a
divergent history and risk clobbering the committed artefacts.

```bash
git fetch origin
git pull --ff-only origin "$(git rev-parse --abbrev-ref HEAD)"
# only now: edit, commit, push
```

If `--ff-only` fails, the remote has CI commits you don't — rebase onto them
(`git rebase origin/<branch>`), never force-push over them.

## Quick reference (gh CLI)

| Goal | Command |
|------|---------|
| Recent runs for a branch | `gh run list --branch <branch> --limit 5` |
| Run + job/step tree | `gh run view <run-id>` |
| **Only the failed steps' logs** | `gh run view <run-id> --log-failed` |
| Per-job conclusions (scriptable) | `gh run view <run-id> --json jobs -q '.jobs[] \| "\(.conclusion // .status)\t\(.name)"'` |
| Block until done + real exit code | `gh run watch <run-id> --exit-status` |
| Re-run only failed jobs | `gh run rerun <run-id> --failed` |
| Trigger manually | `gh workflow run <file.yml>` (or push a file matching `on.push.paths`) |

## The loop

1. **Find the failing run and its failed jobs.** `gh run view <id>` shows the
   job/step tree; note which jobs are `X` and where the `X` step sits.

2. **Extract the real error.** A failed step aborts right above its
   `##[error]Process completed with exit code N` marker, so the most reliable
   method is: strip the known noise, then read the **tail** of the failed steps.
   This surfaces *any* failure — keyword or not:
   ```bash
   gh run view <id> --log-failed \
     | sed -E 's/^[^\t]+\t[^\t]+\t[0-9T:.Z-]+ //' \
     | grep -viE "Node.js 20|FORCE_JAVASCRIPT|github.blog|imported more than once|Stylesheet module|This is permitted|Warning at xsl:stylesheet" \
     | tail -40
   ```
   For a quicker scan, grep the failure anchors with context **before** them (the
   cause usually precedes the marker, so `-B` matters):
   ```bash
   ... | grep -B8 -iE "##\[error\]|exit code|make(\[[0-9]+\])?: \*\*\*|Exception|Traceback|Error at|Could not|Unable|No such"
   ```
   The keyword grep is a convenience, **not** a guarantee: failures with no
   keyword (a custom `xsl:message`, `command not found`, `Permission denied`)
   won't match it — when it yields nothing useful, fall back to the tail. Then
   distinguish the **fatal** line (`make: *** … Error N`, `exit code N`) from the
   many XSLT warnings above it.

3. **Classify the failure** (this decides who fixes it and how):
   - **Config/parity bug** — e.g. `XPST0008 Variable X not declared`: your config
     doesn't match the engine. Fix the config.
   - **Tooling/setup gap** — e.g. `Unable to access jarfile robot/robot.jar`: a
     `make get-*` step is missing from the job. Fix the workflow.
   - **Input/data defect** — e.g. `duplicate key`, `relation is invalid`: the XMI
     model is incompatible. Needs a model decision (don't hand-edit blindly).
   - **Network/import resolution** — e.g. `UnloadableImportException`: an
     `owl:imports` IRI can't be dereferenced. Fix `imports.xml` or the catalog.
   - **Environment/settings** — e.g. `Get Pages site failed`: a repo setting
     (Pages, secrets) — not a code bug.
   - **First-run bootstrap** — e.g. diff `No preexisting files to compare to`:
     expected until a baseline exists; may be acceptable.

4. **Verify the hypothesis against source before editing.** Read the engine
   `Makefile`, the referenced `.xsl`, or the workflow YAML and confirm the cause.
   Compare your config against the engine's known-good reference config.

5. **Apply the minimal fix, then pull-then-commit-then-push.** Change only what
   the error implies. `git pull --ff-only` first (see above), then push.

6. **Re-trigger correctly.** If the workflow is path-filtered (`on.push.paths`),
   a commit that doesn't touch a matching path **won't run** — use
   `gh workflow run`, `gh run rerun --failed`, or extend the path filter.

7. **Watch to completion and repeat.** Fixing one error commonly surfaces the
   next stage's error. Loop until green (or down to accepted failures).

## Common mistakes

- **Editing before pulling** — CI committed artefacts; your push is rejected or
  history diverges. Always pull first.
- **`gh run watch ... | tail`** masks gh's exit code (you read `tail`'s). Read
  job conclusions explicitly with `--json jobs`.
- **`gh run list --limit 1` right after a push** can return the *previous* run.
  Confirm with `headSha` before watching.
- **Changing more than the error implies** — fix the one declared variable / one
  missing target, not the whole file.
- **Treating warnings as errors** — find the line that actually aborts the step
  (`make: *** […] Error N`, `Process completed with exit code N`).
- **Forgetting path filters** — your fix commits but no run starts.

## Real-world impact

A model2owl port failed at 3 jobs; the chain to green was: missing config
variable → adopt compatible input model → add `get-robot` setup → drop
unresolvable SHACL imports → point the import catalog at the right OWL dir → add
config paths to the trigger filter. Each fix exposed the next; none was visible
until the prior one passed.
