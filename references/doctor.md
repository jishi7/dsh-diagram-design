# Environment doctor

Load this file when the user asks for diagnostics, a health check, or first-run troubleshooting ("is this working?",
"why did export fail?", "check the setup").

The goal is a one-shot, read-only report of local readiness for Diagram Design import/export, without mutating user
files or installing dependencies.

Resolve the installation from the `Base directory` line of this skill's load result, never from the user's current
working directory. Running the doctor from an ordinary project directory is expected and is not an error.

## Inputs

Optional flags, in natural language ("strict", "as JSON") or as written here:

- `--strict` — treat warnings as failures in the final summary.
- `--json` — append a machine-readable JSON report to the human summary.

No flags means standard mode.

## Required checks

Run in this order; report each as `pass`, `warn`, or `fail`.

1. **Python runtime**
- Probe `python` first, then `python3`. A name on PATH that cannot report its version is not a valid winner — fall
  through to the next candidate. (Windows ships a `python3` App Execution Alias that is a Microsoft Store stub, not an
  interpreter; it often sits on PATH ahead of a real install.) A name that cannot launch at all is a dud of the same
  kind, reported as a failed check rather than ending the run.
- Require version >= 3.10.
- `fail` when no interpreter is found, or the best one is older than 3.10.

2. **Playwright availability for PNG export**
- Check whether `import playwright` works in the winning interpreter.
- Prove the default headless Chromium can launch and close: bound startup to 5 seconds and the probe subprocess to 10
  seconds, then terminate the probe tree on timeout (Windows tree cleanup and parent reaping get 2 extra seconds;
  report cleanup failures as warnings).
- Missing Playwright is a `warn`, with the remediation command
  `python -m pip install playwright && python -m playwright install chromium`.
- Never auto-install anything.

3. **Bundle integrity**
- Confirm `SKILL.md` sits directly under the resolved base directory.
- Confirm `scripts/self_check.py`, `scripts/export_svg.py`, `scripts/export_png.py`, `scripts/drawio_extract.py`,
  `scripts/mermaid_extract.py` and `scripts/excalidraw_extract.py` exist there. Missing files are `fail` with a
  "reinstall or update the skill" suggestion.
- Confirm `references/` and `assets/` exist (missing either is `warn`: the skill still loads, but routing or templates
  will fail on the affected type).

4. **Path hygiene**
- Quote any resolved path containing spaces in copy-pastable commands.
- If the installation is a junction or symlink, report both the visible path and its target; broken links are `fail`.
- Do not walk the user's project looking for `SKILL.md`: one mis-installed copy must not be mistaken for this one.

## Output contract

Always print:

1. One summary line:
   `Doctor summary: <PASS|WARN|FAIL> (<pass_count> pass, <warn_count> warn, <fail_count> fail)`
2. One line per check: `[PASS] Python 3.12.4 at ...`, `[WARN] Playwright not importable ...`,
   `[FAIL] Missing scripts/export_png.py`.
3. A `Next actions` section only when a warn or fail exists, with copy-pastable fixes.
4. With `--json`, a JSON object: `status`, `counts`, `checks[]` (`name`, `status`, `message`, `fix`), `timestamp`.

## Safety and behavior rules

- Read-only: no file writes, no package installs, no destructive git commands.
- If a probe fails unexpectedly, capture stderr and continue the remaining checks.
- Never claim a check passed unless it was verified in this run.
- Report what you saw, not what the documentation says should happen.

## Example result

```text
Doctor summary: WARN (5 pass, 1 warn, 0 fail)
[PASS] Python 3.12.4 at <interpreter path>
[WARN] Playwright not importable in that interpreter (PNG export unavailable)
[PASS] SKILL.md present at base directory
[PASS] 6 helper scripts present
[PASS] references/ and assets/ present

Next actions
- Enable PNG export: python -m pip install playwright && python -m playwright install chromium
```
