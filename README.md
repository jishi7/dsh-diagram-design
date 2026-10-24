# diagram-design — DeepSeek Harness edition

Editorial diagram design as a DSH skill: ask in plain words, get **one self-contained `.html` file** with inline SVG, in
your brand. 45 diagram types, plus import from `.drawio`, Mermaid and `.excalidraw`, and SVG/PNG export.

This is a trimmed, DSH-only remix of [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design)
(MIT). Everything that existed only for other hosts is gone: Claude Code / Codex / Factory / Qoder plugin manifests,
marketplace docs, slash commands, CI workflows, maintainer scripts, ADRs, screenshots and the GitHub Pages gallery.

## Install

DSH discovers a skill as `<root>/<name>/SKILL.md`, one level deep, scanning these roots by priority:

| Rank | Root |
|---|---|
| 100 | `<projectRoot>/.dsh/skills` |
| 200 | `<projectRoot>/.agents/skills` |
| 300 | `customSkillDirs` (config) |
| 400 | `~/.dsh/skills` |
| 500 | `~/.agents/skills` |

This repository is the bundle itself, so a junction (Windows, no admin needed) or symlink is enough:

```powershell
# global: every session in every project
New-Item -ItemType Junction -Path "$HOME\.dsh\skills\diagram-design" -Target "<path-to-this-repo>"

# or project-scoped: only sessions whose project root is <project>
New-Item -ItemType Junction -Path "<project>\.dsh\skills\diagram-design" -Target "<path-to-this-repo>"
```

```bash
# macOS / Linux equivalent
ln -s "<path-to-this-repo>" ~/.dsh/skills/diagram-design
```

No restart is needed: the skill provider watches its roots and republishes the catalog on the next model step. A copy
instead of a link also works — you then update by re-copying.

## Use

Just describe the diagram:

> Make me an architecture diagram of my app: frontend, backend, database, Redis cache.

> I need a quadrant showing Q2 projects by impact vs effort.

> Redraw this Mermaid block for my deck.

The skill is also user-invocable as `/diagram-design`. On first use in a project it asks whether to onboard your brand
(palette + fonts) or keep the default skin.

## Dependencies

| Feature | Needs |
|---|---|
| HTML/SVG output, self-check | Python 3.10+ (`python`) |
| PNG export (`scripts/export_png.py`) | Playwright + Chromium: `python -m pip install playwright && python -m playwright install chromium` |
| Imports | nothing beyond Python |

```bash
python scripts/self_check.py <file.html>        # prints OK, or why not
```

## Layout

```
SKILL.md              routing, design system, checklist, environment notes
references/           63 files: 45 type-*.md + style-guide, semantic patterns, export/import, onboarding, profiles
scripts/              6 Python helpers: self_check, export_svg/png, drawio/mermaid/excalidraw extract
assets/               5 templates, 200 per-type examples, icons.html, index.html gallery
```

`SKILL.md` routes: a request loads one `type-*.md`, plus `primitives-core.md` / `layout-budget.md` only when needed.
Nothing in `references/` is preloaded.

## Update

```bash
git -C "<path-to-this-repo>" pull          # junction keeps working, catalog refreshes itself
```

## Licence

MIT — see [LICENSE](LICENSE). Icon licensing (Tabler MIT, Simple Icons CC0) is recorded in
[THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) and `references/primitive-icons.md`. Original design and text
© Cathryn Lavery; DSH-specific changes (layout, host removal, command references, README) are released under the same
licence.
